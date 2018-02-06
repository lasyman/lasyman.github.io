---
title: Windows进程检测和结束进程
date: 2018-01-02 18:47:43
categories: 进程
tags: windows
---

> 有些软件运行过程中，需要调用一些外部进程进行一些操作，如果外部进程出现异常无法退出，那么就会直接影响主程序的运行，此时就需要主程序主动将这个进程结束掉了。

<!--more-->
**本文主要介绍在Windows下如何查找对应进程并结束掉**

#### 一、通过Windows 命令
```
tasklist /FI "IMAGENAME eq xxx.exe" //查找进程是否存在
taskkill /F /pid %App_PID% //根据进程id结束进程
```

#### 二、通过Windows API
- 查看进程是否存在
```
int processExist(qint64 pid)
{
    int ret = 0;
#if 0
    PROCESSENTRY32 pe32;
    // 在使用这个结构之前，先设置它的大小  
    pe32.dwSize = sizeof(pe32);

    // 给系统内的所有进程拍一个快照  
    HANDLE hProcessSnap = ::CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    if (hProcessSnap == INVALID_HANDLE_VALUE)
    {
        return -1;
    }

    // 遍历进程快照，轮流显示每个进程的信息  
    BOOL bMore = ::Process32First(hProcessSnap, &pe32);
    while (bMore)
    {
        if (pe32.th32ProcessID == pid)
        {
            if (QString::fromWCharArray(pe32.szExeFile) == "lht_process.exe")
            {
                ret = 1;
                break;
            }
        }
        bMore = ::Process32Next(hProcessSnap, &pe32);
    }

    // 不要忘记清除掉snapshot对象  
    ::CloseHandle(hProcessSnap);
#else 
    DWORD idProcess = pid;
    // 打开进程句柄  
    HANDLE hProcess = OpenProcess(PROCESS_QUERY_INFORMATION | PROCESS_VM_READ, FALSE, idProcess);
    if (NULL != hProcess)
    {
        WCHAR sPath[MAX_PATH];
        HMODULE hMod;
        DWORD cbNeeded;
        // 获取路径  
        if (EnumProcessModules(hProcess, &hMod, sizeof(hMod), &cbNeeded))
        {
            DWORD dw = GetModuleFileNameEx(hProcess, hMod, sPath, MAX_PATH);
            QString path = QString::fromWCharArray(sPath);
            QFileInfo fileInfo(path);
            if (fileInfo.fileName() == "lht_process.exe")
            {
                ret = 1;
            }
        }
        CloseHandle(hProcess);
    }
#endif
    return ret;
}
```
- 结束进程
```
bool killProcess(qint64 pid)
{
    DWORD dwPid = pid;
    HANDLE hPrc;
    if (0 == dwPid) return FALSE;

    hPrc = ::OpenProcess(PROCESS_ALL_ACCESS, FALSE, dwPid);  // Opens handle to the process.  
    if (!::TerminateProcess(hPrc, 0)) // Terminates a process.
    {
        ::CloseHandle(hPrc);
        return FALSE;
    }
    else
        ::WaitForSingleObject(hPrc, 5000);

    ::CloseHandle(hPrc);
    return TRUE;
}
```