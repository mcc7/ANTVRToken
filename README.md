# 文件说明
1. ANTVRToken.zip为游戏加密包
2. CheckANTVRToken.zip为游戏加密验证程序

---
# 游戏加密
对应ANTVRToken.zip

## 文档目的
此文档仅供给与蚁视合作的游戏开发厂商阅读使用，用于游戏开发厂商与蚁视保护游戏产权.防止用户在未授权下进行游戏.
  
## 验证步骤
1. 在蚁视大厅启动游戏时以启动参数的形式传入蚁视token(如C://games/game.exe token)
2. 游戏运行后获取启动参数的token进行验证
- 验证方式为:
在运行游戏时调用蚁视提供的ANTVRToken.dll CheckToken函数进行验证
- 函数原型为:
```
/// <summary>
/// 验证token(游戏厂家用)
/// </summary>
/// <param name="token">游戏启动时启动参数</param>
/// <param name="gameid">游戏的唯一id,由蚁视BD提供</param>
/// <returns>验证结果,
0为验证通过
1为输入的token长度错误，应该为包含128个有效字符的字符串
2为输入的游戏gameid错误，应该为9位的int型数字
3为输入的token为无效字符，应该接受从启动参数传来的正确token将其传入接口.
4 为gameid长度正确，但是匹配不成功
5 为超时处理
6 为非同一本地电脑
</returns> 
int CheckToken(const int gameid, char* token)
```

## 集成步骤
注ANTVRToken.dll使用C++编写
1. 根据游戏名向蚁视BD索要对应游戏的gameid,一般为9位数字
2. 将ANTVRToken下的[Windows]目录覆盖到系统盘(ANTVRToken.dll不存在于游戏工程内)
3. 在[#调用示例]中找到对应编程语言Demo进行集成
4. 集成完成后根据[CheckANTVRToken.zip]下的检查工具进行验证(验证说明参照[#游戏验证])
5. 验证通过后将游戏提交于蚁视BD

## 调用示例

### C++版本
```
#include"AntvrToken.h"
#include<iostream>
int main(int argc, char *argv[])
{
char token[129] = { 0 };
if (argc > 1) {  
memcpy(token, argv[1], 128);  
printf("收到的字符流： %s\n", token); 
}else { 
printf("启动失败，无Token");
// Todo:阻止游戏运行的逻辑  
return 0;
} 
// 具体游戏ID请于蚁视BD索要,123456789为示例 
const int gameid = 123456789;
int returnvalue = antvr::antvrtoken::CheckToken(gameid, token);
if (returnvalue == 0)
{
printf("启动成功\n");  
// Todo:继续游戏运行的逻辑
} else 
{
printf("启动失败 Error : %d\n", returnvalue);  
// Todo:阻止游戏运行的逻辑
}
system("pause"); 
return 0;
}
```
### Unity for C#版本
```
class Program
{
void Start()      
{
// 具体游戏ID请于蚁视BD索要,123456789为示例
int gameid = 123456789;
string[] CommandLineArgs = Environment.GetCommandLineArgs();
if (CommandLineArgs.Length < 2)          
{
                Console.WriteLine("启动失败，无Token");
                // Todo:阻止游戏运行的逻辑
                return;          
}
string token = CommandLineArgs[1];           
var result = CheckToken(gameid, token);
if (result == 0)          
{
                Console.WriteLine("启动成功");
                // Todo:继续游戏运行的逻辑          
}       
else          
{
                Console.WriteLine("启动失败Error:",result);
     // Todo:阻止游戏运行的逻辑           
}      
}

[System.Runtime.InteropServices.DllImport("ANTVRToken",
CharSet = System.Runtime.InteropServices.CharSet.Ansi, SetLastError = false,
CallingConvention = System.Runtime.InteropServices.CallingConvention.Cdecl)]

       
public static extern int CheckToken(int gameid, string token);
}
```


### 其他语言

请参照C++或C#语言进行编写
    
## 常见问题

### Dll调用错误
调用失败,提示<无法加载 DLL“ANTVRToken”: 找不到指定的模块。>或者64,32位编码错误等无法正常调用到ANTVRToken.dll的请将蚁视加密包里[Windows]目录直接覆盖到系统盘即可(ANTVRToken.dll不存在于游戏项目工程内).
### Token获取方式错误   
Token是从启动参数里获取,蚁视大厅会以启动参数的形式传入token(如C://games/game.exe token),游戏开发者请确保以正确的方式从启动参数里获取token
### Gameid定义错误   
gameid为蚁视BD针对每款游戏提供的唯一id(一般为9为数字),具体gameid请于蚁视BD索要,随意填写将导致游戏无法启动.
### 其他未知问题
如遇未知的集成问题或文档不清晰导致无法正常集成蚁视加密模块的可联系蚁视BD,由蚁视BD安排相关开发人员协助集成.

---

# 游戏验证

对应CheckANTVRToken.zip
1. 验证程序需要Framework4.5的支持
2. 请保证系统盘Windows目录下有蚁视的加密dll(在AntvrToken.zip包中)

## 验证逻辑

### Windows目录下有蚁视加密dll时 
1. 直接启动游戏成功(游戏加密失败) 
2. 打开验证工具输入错误的游戏id和正确的路径,正常启动游戏(游戏加密失败) 
3. 直接启动游戏失败(游戏加密成功) 
4. 打开验证工具输入正确的游戏id和路径,正常启动游戏(游戏加密成功)
### Windows目录下无蚁视加密dll时,依然可以启动游戏(游戏加密失败)

---


