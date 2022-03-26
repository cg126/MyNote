

# 创建窗口

最流行的几个库有GLUT，SDL，SFML和GLFW。在教程里我们将使用**GLFW**。

头文件：

```C++
// glad必须在GLFW前面
#include <glad/glad.h>
#include <GLFW/glfw3.h>
```

创建main函数

```C++
int main()
{
    glfwInit();		// 初始化glfw
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    // glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);		// Mac OS X系统需添加此句
    
    return 0;
}
```

创建窗口对象，这个窗口对象存放了所有和窗口相关的数据，而且会被GLFW的其他函数频繁地用到。

```C++
GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);
if (window == NULL)
{
    std::cout << "Failed to create GLFW window" << std::endl;
    glfwTerminate();
    return -1;
}
glfwMakeContextCurrent(window);
```

## GLAD

GLAD是用来管理OpenGL的函数指针的，所以在调用任何OpenGL的函数之前我们需要初始化GLAD。

```C++
if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
{
    std::cout << "Failed to initialize GLAD" << std::endl;
    return -1;
}
```

## 视口

必须告诉OpenGL渲染窗口的尺寸大小，即视口(Viewport)

```C++
glViewport(0, 0, 800, 600);
```

创建一个回调函数，它会在每次窗口大小被调整的时候被调用。

```C++
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}
```

还需调用一个函数，告诉GLFW每当窗口调整大小的时候调用上面的函数：

```C++
glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
```

## 渲染循环

```C++
while (!glfwWindoShouldClose(window))
{
    glfwSwapBuffers(window);	// 交换颜色缓冲
    glfwPollEvents();			// 检查是否触发什么事件（比如键盘输入、鼠标移动等）、更新窗口状态，并调用对应的回调函数
}
```

## 释放资源

```C++
glfwTerminate();
return 0;
```

## 输入控制

在GLFW中实现一些输入控制

```C++
void processInput(GLFWwindow *window)
{
    // 如果按下了esc键，通过glfwSetwindowShouldClose将`WindowShouldClose`属性设置为true，从而关闭glfw
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}
```

渲染代码改为：

```C++
while (!glfwWindowShouldClose(window))
{
    processInput(window);
    
    glfwSwapBuffers(window);
    glfwPollEvents();
}
```

## 渲染

把所有的渲染(Rendering)操作放到渲染循环中

```C++
// 渲染循环
while(!glfwWindowShouldClose(window))
{
    // 输入控制
    processInput(window);

    // 渲染指令
    ...

    // 检查并调用事件，交换缓冲
    glfwPollEvents();
    glfwSwapBuffers(window);
}
```

清空颜色缓冲

```C++
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);	// （状态设置函数）设置颜色
glClear(GL_COLOR_BUFFER_BIT);			// （状态使用函数）使用设置的颜色填充屏幕
```



# 显示三角形

## 准备知识

- 顶点数组对象：Vertex Array Object, VAO
- 顶点缓冲对象：Vertex Buffer Object, VBO
- 索引缓冲对象：Element Buffer Object, EBO或Index Buffer Object, IBO

OpenGL的大部分工作是把3D坐标转换为2D像素，这个过程是由 图形渲染管线（Graphics Pipeline）管理的。

图形渲染管线主要分为两个部分：1. 将3D坐标转为2D坐标；2. 将2D坐标转为有颜色的像素。

图形渲染管线的每个阶段的抽象展示：

<img src="C:\Users\17811\AppData\Roaming\Typora\typora-user-images\image-20211118160246995.png" alt="image-20211118160246995" style="zoom:67%;" />

顶点数据(Vertex Data)：一系列点的集合

图形渲染管线的组成部分：

1. **顶点着色器(Vertex Shader)：**将3D坐标转换为另一种3D坐标，同时对顶点属性进行一些基本处理。
2. 图元装配(Primitive Assembly)：将所有的点装配成指定图元的形状
3. 几何着色器(Geometry Shader)：产生新顶点构造出新的（或是其它的）图元来生成其他形状
4. 光栅化阶段(Rasterization Stage)：把图元映射为最终屏幕上相应的像素，并裁切超出视图以外的像素，来提升执行效率
5. **片段着色器：**计算像素的最终颜色
6. Alpha测试和混合(Blending)：检测片段对应的深度值，判断此像素在其他物体的前面还是后面，已决定其是否被丢弃。

在现代OpenGL中，必须定义至少一个顶点着色器和一个片段着色器。

## 顶点输入

将三角形的三个顶点以标准化设备坐标形式定义为一个float数组。

```c++
float vertices[] = {
    -0.5f, -0.5f, 0.0f,
     0.5f, -0.5f, 0.0f,
     0.0f,  0.5f, 0.0f
};
```

因为要渲染一个2D三角形，将顶点的z坐标设置为0.0。

**标准化设备坐标**

![image-20211118162117393](C:\Users\17811\AppData\Roaming\Typora\typora-user-images\image-20211118162117393.png)

创建 **顶点缓冲对象(VBO)** 管理顶点数据

```C++
unsigned int VBO;
glGenBuffers(1, &VBO);
```

使用glBindBuffer函数把新创建的缓冲绑定到GL_ARRAY_BUFFER目标上

```C++
glBindBuffer(GL_ARRAY_BUFFER, VBO);  
```

调用glBufferData函数，把之前定义的顶点数据复制到缓冲的内存中

```C++
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

顶点着色器

需要至少设置一个顶点着色器和片段着色器。

顶点着色器源码：

```C++
#version 330 core
layout (location = 0) in vec3 aPos;		// layout (location = 0)设定了输入变量的位置；in关键字声明所有的顶点属性，此处创建了一个vec3输入向量aPos

void main()
{
    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```

将顶点着色器的源代码硬编码在代码文件顶部的C风格字符串中

```C++
const char *vertexShaderSource = "#version 330 core\n"
    "layout (location = 0) in vec3 aPos;\n"
    "void main()\n"
    "{\n"
    "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
    "}\0";
```

必须动态编译上面的源代码

用glCreateShader创建着色器对象

```C++
unsigned int vertexShader;
vertexShader = glCreateShader(GL_VERTEX_SHADER);	// 由于正在创建一个顶点着色器，传递的参数是GL_VERTEX_SHADER
```

**编译着色器**

下一步，把这个着色器源码附加到着色器对象上，然后编译它

```C++
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);	// 第一个参数是要编译的着色器对象，第二个是源码字符串数量，第三个是着色器的源码
glCompileShader(vertexShader);
```

## 片段着色器

片段着色器要做的是计算像素最后的颜色输出。

片段着色器源码：

```C++
#version 330 core
Out vec4 FragColor;

void main()
{
    FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
}
```

编译片段着色器

```C++
// 创建片段着色器对象
unsigned int fragmentShader;
fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);

glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
glCompileShader(fragmentShader);
```

接下来把两个着色器对象 **链接** 到一个用来渲染的 **着色器程序(Shader Program)** 中。

## 着色器程序

创建一个着色器程序对象：

```C++
unsigned int shaderProgram;
shaderProgram = glCreateProgram();
```

把之前编译的着色器附加到程序对象上，然后用glLinkProgram链接它们

```C++
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);
```

调用glUseProgram函数，用刚创建的程序对象作为它的参数，以激活这个程序对象

```C++
glUseProgram(shaderProgram);
```

在把着色器对象链接到程序对象以后，删除着色器对象

```C++
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);
```

## 链接 顶点属性

须手动指定输入数据的哪一个部分对应顶点着色器的哪一个顶点属性

顶点缓冲数据会被解析为如下：

![image-20211118215028117](C:\Users\17811\AppData\Roaming\Typora\typora-user-images\image-20211118215028117.png)

使用glVertexAttribPointer函数告诉OpenGL该如何解析顶点数据

```C++
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
```

参数解释如下：

1. 指定要配置的顶点属性。此处为顶点着色器中使用layout(location = 0)定义的position顶点属性的位置值(Location)。
2. 顶点属性的大小。顶点属性是一个vec3，有三个值，大小是3.
3. 指定数据的类型
4. 是否希望数据被标准化
5. 步长
6. 表示位置数据在缓冲中起始位置的偏移量(Offset)

# 着色器

从基本意义上来说，着色器只是一种把输入转化为输出的程序。着色器也是一种非常独立的程序，因为它们之间不能相互通信；它们之间唯一的沟通只有通过输入和输出。

## GLSL

着色器是使用一种叫GLSL的类C语言写成的。

典型的着色器结构：

```C
#version version_number
in type in_variable_name;
in type in_variable_name;

out type out_variable_name;

uniform type uniform_name;

int main()
{
    // 处理输入并进行一些图形操作...
    
    // 输出处理过的结果到输出变量
    out_variable_name = weird_stuf
}
```

## 数据类型

GLSL中包含C等其它语言大部分的默认基础数据类型：`int`、`float`、`double`、`uint`和`bool`。

GLSL有两种容器类型：`向量（Vector）`和`矩阵（Matrix）`

### 向量

- vecn：包含`n个`float分量的默认向量（大多数时候使用这个）
- bvecn：bool
- ivecn：int
- uvecn：unsigned int
- dvecn：double

可以分别使用`.x`、`.y`、`.z`和`.w`来获取它们的第1、2、3、4个分量。GLSL也允许你对颜色使用`rgba`，或是对纹理坐标使用`stpq`访问相同的分量。

重组：

```C++
vec2 someVec;
vec4 differentVec = someVec.xyxx;
vec3 anotherVec = differentVec.zyw;
vec4 otherVec = someVec.xxxx + anotherVec.yxzy
```

将一个向量作为参数传递给不同的向量构造函数：

```C++
vec2 vect = vec2(0.5, 0.7);
vec4 result = vec4(vect, 0.0, 0.0);
vec4 otherResult = vec4(result.xyz, 1.0);
```

## 输入与输出

GLSL定义了`in`和`out`关键字使得着色器之间进行数据交流和传递。

顶点着色器的输入特殊在，它从顶点数据中直接接收输入。为了定义顶点数据该如何管理，我们使用`location`这一元数据指定输入变量，这样我们才可以在CPU上配置顶点属性。

片段着色器需要一个`vec4`颜色输出变量，因为片段着色器需要生成一个最终输出的颜色。如果你在片段着色器没有定义输出颜色，OpenGL会把你的物体渲染为黑色（或白色）。

**顶点着色器：**

```C
#version 330 core
layout (location = 0) in vec3 aPos;	// 位置变量的属性值设为0

out vec4 vertexColor;	// 为片段着色器指定一个颜色输出

void main()
{
    gl_Position = vec4(aPos, 1.0);	// 三维向量aPos作为了构造函数的参数
    vertexColor = vec4(0.5, 0.0, 0.0, 1.0);	// 将输出变量设置为暗红色
}
```

**片段着色器：**

```C
#version 330 core
out vec4 FragColor;

in vec4 vertexColor;	// 从顶点着色器传入的变量

void main()
{
    FragColor = vertexColor;
}
```

## Uniform

从应用程序中直接给片段着色器发送一个颜色！

在一个着色器中添加`uniform`关键字至类型和变量名前来声明一个GLSL的uniform：

```C++
#version 330 core
out vec4 FragColor;

uniform vec4 ourColor;	// 在OpenGL程序代码中设定此变量

void main()
{
    FragColor = ourColor;
}
```

让它随着时间改变颜色：

```C++
float timeValue = glfwGetTime();		// 获取运行的秒数
float greenValue = (sin(timeValue) / 2.0f) + 0.5f;	// sin函数让颜色在0到1之间改变
int vertexColorLocation = glGetUniformLocation(shaderProgram, "ourColor");	// glGetUniformLocation查询uniform ourColor的位置值
glUseProgram(shaderProgram);			// 更新一个uniform之前必须先使用程序（调用glUseProgram）
glUniform4f(vertexColorLocation, 0.0f, greenValue, 0.0f, 1.0f);	// 设置uniform的`4个float值`
```

使颜色慢慢变化：

```C++
while (!glfwWindowShouldClose(window))
{
    processInput(window);	// 输入控制
    
    glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);
    
    glUseProgram(shaderProgram);
    
    float timeValue = glfwGetTime();
    float greeValue = (sin(timeValue) / 2.0f) + 0.5f;
    int vertexColorLocation = glGetUniformLocation(shaderProgram, "ourColor");
    glUniform4f(vertexColorLocation, 0.0f, greenValue, 0.0f, 1.0f);
    
    // 绘制三角形
    glBindVertexArray(VAO);
    glDrawArrays(GL_TRIANGLES, 0, 3);
    
    // 交换缓冲并查询IO事件
    glfwSwapBuffers(window);
    glfwPollEvents();			// 检查是否有触发事件，并调用相应的回调函数
}
```

## 设定更多属性

将三角形的三个角分别指定为红、绿、蓝：

```C++
float vertices[] = {
     0.5f, -0.5f, 0.0f,	1.0f, 0.0f, 0.0f,	// 右下
    -0.5f, -0.5f, 0.0f,	0.0f, 1.0f, 0.0f,	// 左下
     0.0f, 0.5f, 0.0f,	0.0f, 0.0f, 1.0f	// 顶部
};
```

调整一下顶点着色器，使它能够接收颜色值作为一个顶点属性输入。

```C++
#version 330 core
layout (location = 0) in vec3 aPos;		// 位置变量的属性值为 0
layout (location = 1) in vec3 aColor;	// 颜色变量的属性值为 1

out vec3 ourColor;	// 向片段着色器输出一个颜色

void main()
{
    gl_Position = vec4(aPos, 1.0);
    ourColor = aColor;	// 将OurColor设置为从顶点数据得到的输入颜色
}
```

片段着色器：

```C++
#version 330 core
out vec4 FragColor;
in vec3 ourColor;

void main()
{
    FragColor = vec4(ourColor, 1.0);	// 为什么gl_Posision FragColor是四维的？
}
```

由于添加了另一个顶点属性，必须重新配置顶点属性指针。使用glVertexAttribPointer函数更新顶点格式：

```C++
// 位置属性
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// 颜色属性
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);
```



# Assimp

全称：Open Asset Import Library

导入多种不同的模型文件格式（并也能够导出部分的格式），将所有模型数据加载至Assimp的通用数据结构中。使能够以同一种方式访问需要的数据。

## **网格**

一个模型通常由几个子模型组合而成。一个网格是OpenGL中绘制物体所需的最小单位（顶点数据、索引和材质属性）。一个模型（通常）会包括多个网格。

一个网格至少需要一系列的顶点，每个顶点包含一个位置向量、一个法向量和一个纹理坐标向量。

在OpenGL中定义一个顶点：

Vertex结构体：

```C++
struct Vertex
{
    glm::vec3 Position;	// 位置向量
    glm::vec3 Normal;	// 法向量
    glm::vec2 TexCoords;// 纹理坐标向量
}
```

将纹理数据整理到Texture结构体中：

```C++
struct Texture
{
    unsigned int id;	// 纹理id
    string type;		// 纹理类型
}
```

定义网格类的结构：

```C++
class Mesh
{
public:
    // 网格数据
    vector<Vertex> vertices;		// 由顶点结构体组成的向量
    vector<unsigned int> indices;
    vector<Texture> textures;		// 由材质结构体组成的向量
    
    Mesh(vector<Vertex> vertices, vector<unsigned int> indices, vector<Texture> textures);	// 构造函数
    void Draw(Shader shader);	// 使用Draw函数绘制网格
private:
    // 渲染数据
    unsigned int VAO, VBO, EBO;
    
    void setupMesh();
}
```

Mesh():

```C++
Mesh(vector<Vertex> vertices, vector<unsigned int> indices, vector<Texture> textures)
{
    this->vertices = vertices;
    this->indices = indices;
    this->textures = textures;
    
    setupMesh();	// 在构造函数中调用setupMesh()
}
```

### 初始化

在渲染之前必须配置正确的缓冲，并通过顶点属性指针定义顶点着色器的布局。

```C++
void setupMesh()
{
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glGenBuffers(1, &EBO);
    
    glBindVertexArray(VAO);
    // pass..
}
```

### 渲染

设定一个命名标准：每个漫反射纹理被命名为`texture_diffuseN`，每个镜面光纹理应该被命名为`texture_specularN`

渲染代码：

```C++
void Draw(Shader shader)
{
    unsigned int diffuseNr = 1;
    unsigned int specularNr = 1;
    for(unsigned int i=0; i<textures.size(); i++)
    {
        glActiveTexture(GL_TEXTURE0 + i);	// 在绑定之前激活相应的纹理单元
        // 获取纹理序号（diffuse_textureN 中的 N）
        string number;
        string name = textures[i].type;
        if (name == "texture_diffuse")
            number = std::to_string(diffuseNr++);
        else if (name == "texture_specular")
            number = std::to_string(specularNr++);
        
        shader.setFloat(("material." + name + number).c_str(), i);
        glBindTexture(GL_TEXTURE_2D, textures[i].id);
    }
    glActivaTexture(GL_TEXTURE0);
    
    // 绘制网格
    glBindVertexArray(VAO);
    glDrawElements(GL_TRIANGLES, indices.size(), GL_UNSIGNED_INT, 0);
    glBindVertexArray(0);
}
```

## 模型

模型作为多个网格对象的容器，能够实现Assimp的加载接口。

使用Assimp加载模型，并将它转换至多个Mesh对象中。

```C++
class Model
{
public:
    Model(char *path)
    {
        loadModel(path);	// 在构造函数中加载文件
    }
    void Draw(Shader shader);	
private:
    vector<Mesh> meshes;		// 由Mesh类组成的向量
    string directory;
    // 以下函数用于处理导入过程
    void loadModel(string path);
    void processNode(aiNode *node, const aiScene *scene);
    Mesh processMesh(aiMesh *mesh, const aiScene *scene);
    vector<Texture> loadMaterialTextures(aiMaterial *mat, aiTextureType type, string typeName);
}
```

Draw:

```C++
void Draw(Shader shader)
{
    // 遍历所有网格，并调用它们各自的Draw函数
    for(unsigned int i=0; i<meshes.size(); i++)
        meshes[i].Draw(shader);
}
```

### 导入3D模型到OpenGL

Assimp头文件

```C++
#include <assimp/Importer.hpp>
#include <assimp/scene.h>
#include <assimp/postprocess.h>
```

在loadModel中，使用Assimp来加载模型至scene的数据结构中。有了这个场景对象，我们就能访问到加载后的模型中所有所需的数据了。

对于所有文件的加载代码都相同：

```C++
Assimp::Importer importer;
const aiScene *scene = importer.ReadFile(path, aiProcess_Triangulate | aiProcess_FlipUVs);	// aiProcess_Triangulate将模型所有的图元形状变换为三角形；aiProcess_FlipUVs将在处理的时候翻转y轴的纹理坐标
```

难点在于将加载的数据转换到一个Mesh对象的数组。

loadModel()：

```C++
void loadModel(string path)
{
    Assimp::Importer import;
    const aiScene *scene = import.ReadFile(path, aiProcess_Triangulate | aiProcess_FlipUVs);
    
    if (!scene || scene->mFlags & AI_Scene_FLAGS_INCOMPLETE || !scene->mRootNode)
    {
        cout << "ERROR::ASSIMP::" << import.GetErrorString() << endl;
        return;
    }
    directory = path.substr(0, path.find_last_of('/'));
    
    processNode(scene->mRootNode, scene);	// 没有错误发生时，将处理场景中的所有点
}
```

processNode()：

递归调用此函数

```C++
void processNode(aiNode *node, const aiScene *scene)
{
    // 处理根节点所有的网格
    for (unsigned int i=0; i<node->mNumMeshes; i++)
    {
        aiMesh *mesh = scene->mMeshes[node->mMeshes[i]];
        meshes.push_back(processMesh(mesh, scene));
    }
    // 对根节点的子节点重复此过程
    for (unsigned int i=0; i<node->mNumChildren; i++)
    {
        processNode(node->mChildren[i], scene);
    }
}
```

处理完网格后，将遍历节点的子节点，并调用相同的processNode函数。

下一步是将Assimp的数据解析到网格Mesh类中。

#### 从Assimp到网格

我们要做的只是访问网格的相关属性并将它们储存到我们自己的对象中。

processMesh()：

```C++
Mesh processMesh(aiMesh *mesh, const aiScene *scene)
{
    vector<Vertex> vertices;
    vector<unsigned int> indices;
    vector<Texture> textures;
    
    for (unsigned int i=0; i<mesh->mNumVertices; i++)
    {
        Vertex vertex;
        // 处理顶点位置、法线和纹理坐标...
        
        vertices.push_back(vertex);
    }
    // 处理索引...
    // 处理材质...
    if (mesh->mMaterialIndex >= 0)
    {
        // ...
    }
    
    return Mesh(vertices, indices, textures);
}
```

顶点位置处理：

```C++
glm::vec3 vector;		// 定义一个vec3临时变量
vector.x = mesh->mVertices[i].x;
vector.y = mesh->mVertices[i].y;
vector.z = mesh->mVertices[i].z;
vertex.Position = vector;
```

法线处理：

```C++
vector.x = mesh->mNormals[i].x;
vector.y = mesh->mNormals[i].y;
vector.z = mesh->mNormals[i].z;
vertex.Normal = vector;
```

纹理坐标处理：

```C++
if (mesh->mTextureCoords[0])
{
    glm::vec2 vec;
    vec.x = mesh->mTextureCoords[0][i].x;
    vec.y = mesh->mTextureCoords[0][i].y;
    vertex.TexCoords = vec;
}
else
    vertex.TexCoords = glm::vec2(0.0f, 0.0f);
```

vertex结构体现在已经填充好了需要的顶点属性，最后会将它压入vertices这个vector的尾部。这个过程会对每个网格的顶点都重复一遍。

#### 索引

Assimp的接口定义了每个网格都有一个面（Face）数组，每个面代表一个图元，在此案例中为三角形（因为使用了aiProcess_Triangulate选项）。一个面包含多个索引。

```C++
for(unsigned int i=0; i<mesh->mNumFaces; i++)
{
    aiFace face = mesh->mFaces[i];
    for (unsigned int j=0; j<face.mNumIndices; j++)
        indices.push_back(face.mIndices[j]);
}
```

现在有了一系列的顶点和索引数据，它们可以通过glDrawElements函数来绘制网格。

#### 材质

和节点一样，一个网格只包含了一个指向材质对象的索引。如果想要获取网格真正的材质，我们还需要索引场景的mMaterials数组。网格材质索引位于它的mMaterialIndex属性中，我们同样可以用它来检测一个网格是否包含有材质：

```C++
if (mesh->mMaterialIndex >= 0)
{
    aiMaterial *material = scene->mMaterials[mesh->mMaterialIndex];
	// 加载漫反射贴图
    vector<Texture> diffuseMaps = loadMaterialTextures(material, aiTextureType_DIFFUSE, "texture_diffuse");
    textures.insert(textures.end(), diffuseMaps.begin(), diffuseMaps.end());
    // 加载镜面光贴图
    vector<Texture> specularMaps = loadMaterialTextures(material, aiTextureType_SPECULAR, "texture_specular");
    textures.insert(textures.end(), specularMaps.begin(), specularMaps.end());
}
```

loadMaterialTextures函数遍历了给定纹理类型的所有纹理位置，获取了纹理的文件位置，并加载并和生成了纹理，将信息储存在了一个Vertex结构体中。

```C++
vector<Texture> loadMaterialTextures(aiMaterial *mat, aiTextureType type, string typeName)
{
    vector<Texture> textures;
    for (unsigned int i=0; i<mat->GetTextureCount(type); i++)	// GetTextureCount()检查存储在材质中纹理的数量
    {
        aiString str;
        mat->GetTexture(type, i, &str);			// GetTexture()获取每个纹理的文件位置，其将结果存储在一个`aiString`中
        Texture texture;
        texture.id = TextureFromFile(str.C_Str(), directory);
        texture.type = tyeName;
        texture.path = str;
        textures.push_back(texture);
    }
    
    return textures;
}
```

### 重大优化

当我们想加载一个纹理的时候，首先去检查它有没有被加载过。如果有的话，我们会直接使用那个纹理，并跳过整个加载流程。

```C++
struct Texture
{
    unsigned int id;
    string type;
    aiString path;		// 存储纹理的路径用于与其他纹理进行比较。
};
```

将所有加载过的纹理存储在另一个vector中，在模型类的顶部声明为一个私有变量：

```C++
vector<Texture> textures_loaded;
```

在loadMaterialTextures函数中，将纹理的路径与textures_loaded中的所有纹理进行比较，看是否相同。

```C++
vector<Texture> loadMaterialTextures(aiMaterial *mat, aiTextureType type, string typeName)
{
    vector<Texture> textures;
    for (unsigned int i=0; i<mat->GetTextureCount(type); i++)
    {
        aiString str;
        mat->GetTexture(type, i, &str);
        bool skip = false;
        for (unsigned int j=0; j<textures_loaded.size(); j++)
        {
            if (std::strcmp(textures_loaded[j].path.data(), str.C_str()) == 0)
            {
                textures.push_back(textures_loaded[j]);
                skip = true;
                break;
            }
        }
        if (!skip)
        {
            // 如果纹理没有被加载，则加载它
            Texture texture;
            texture.id = TextureFromFile(str.C_Str(), directory);
            texture.type = typeName;
            texture.path = str.C_Str();
            textures.push_back(texture);
            textures_loaded.push_back(texture);	// 添加到已加载的纹理中
        }
    }
    return textures;
}
```

