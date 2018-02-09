---
title: 使用hook获取键盘输入
date: 2018-01-25 19:47:43
categories: C++
tags: hook
---

> 有时候程序需要实时检测用户的键盘输入，比如说用户通过扫码枪输入条码的时候。当程序是后台运行时，如果程序不检测键盘输入是无法获取到条码的。

<!--more-->

**本文讲介绍如何通过windows hook API获取键盘的实时输入**
示例代码：

```
#include <Windows.h>

HHOOK keyHook = NULL;
// 安装键盘hook
void installKeyboardHook()
{
	// 注册回调 keyboardCallback
	keyHook = SetWindowsHookEx(WH_KEYBOARD_LL, keyboardCallback, GetModuleHandle(NULL), 0);
}
// 卸载键盘hook
void uninstallKeyboardHook()
{
	UnhookWindowsHookEx(keyHook);
}
// 按键事件回调处理
LRESULT CALLBACK keyboardCallback(int nCode, WPARAM wParam, LPARAM lParam)
{
	if (nCode >= 0 && wParam == WM_KEYDOWN)
    {
        //在WH_KEYBOARD_LL模式下lParam 是指向KBDLLHOOKSTRUCT类型地址
        KBDLLHOOKSTRUCT *pkbhs = (KBDLLHOOKSTRUCT *)lParam;
        //如果nCode等于HC_ACTION则处理该消息，如果小于0，则钩子子程就必须将该消息传递给 CallNextHookEx
        QString flag, key;
        if (nCode == HC_ACTION)
        {
            if (pkbhs->flags & LLKHF_ALTDOWN)
                flag = "Alt";
            else if (GetAsyncKeyState(VK_CONTROL) & 0x8000)
                flag = "Ctrl";
            else if (GetAsyncKeyState(VK_SHIFT) & 0x8000)
                flag = "Shift";

            if (pkbhs->vkCode >= VK_F1 && pkbhs->vkCode <= VK_F12)
				qDebug() << "功能键";
            else if (pkbhs->vkCode >= VK_OEM_1 && pkbhs->vkCode < VK_OEM_8)
                qDebug() << "其他键";
            else if (pkbhs->vkCode >= VK_NUMPAD0 && pkbhs->vkCode <= VK_DIVIDE)
                qDebug() << "小键盘"
            else
                qDebug() << "普通按键";

            return 0;//返回1表示截取消息不再传递,返回0表示不作处理,消息继续传递
        }
    }
    return CallNextHookEx(keyHook, nCode, wParam, lParam);
}
```
