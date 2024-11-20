# [VisualStudio Invalid option '7.3' for /langversion; must be ISO-1, ISO-2, Default or an integer in range 1 to 6.](https://github.com/bigbosschenyibo/gitblog/issues/16)

使用Vs2022新建项目，PropertyGroup节点下的LangVersion如下：
``` xml
  <PropertyGroup Condition="'$(Configuration)|$(Platform)' == 'Release|x64'">
    <OutputPath>..\..\Bin\Release\</OutputPath>
    <DefineConstants>TRACE</DefineConstants>
    <Optimize>true</Optimize>
    <DebugType>pdbonly</DebugType>
    <PlatformTarget>x64</PlatformTarget>
    <LangVersion>7.3</LangVersion>
    <ErrorReport>prompt</ErrorReport>
  </PropertyGroup>
```
然后使用Vs2015打开此项目，编译不通过：
解决办法：
### 手动改成default
``` xml
  <PropertyGroup Condition="'$(Configuration)|$(Platform)' == 'Release|x64'">
    <OutputPath>..\..\Bin\Release\</OutputPath>
    <DefineConstants>TRACE</DefineConstants>
    <Optimize>true</Optimize>
    <DebugType>pdbonly</DebugType>
    <PlatformTarget>x64</PlatformTarget>
    <LangVersion>default</LangVersion>
    <ErrorReport>prompt</ErrorReport>
  </PropertyGroup>
```
### 在出问题版本的VS IDE 上，右键项目 -> 属性 -> 生成 -> 高级
将语言版本修改为【Default】，再重新生成，顺利解决
![image](https://github.com/user-attachments/assets/00c2317b-028e-4344-8cae-d9e6d6474278)

