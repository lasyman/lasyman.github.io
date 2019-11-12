---
title: Inno Setup检测进程并结束进程
date: 2018-03-22 11:23:17
categories: 打包exe
tags: windows
---

> 用Inno Setup打包好的执行程序，如果在程序运行过程中更新，就会出现程序被占用导致更新失败，那么就需要在打包脚本中加入检测程序是否运行的功能，然后提示用户是否结束进程并继续安装。
> 本文结合遇到的问题，和网上的资料总结如下：

<!--more-->

## 一、引入psvince.dll 进行判断
将psvince.dll放到脚本所在的目录即可

```
[Files]
Source: psvince.dll; DestDir: {app}

[Code]
// function IsModuleLoaded to call at install time
// added also setuponly flag
function IsModuleLoaded(modulename: String ):  Boolean;
external 'IsModuleLoaded@files:psvince.dll stdcall setuponly';

// function IsModuleLoadedU to call at uninstall time
// added also uninstallonly flag
function IsModuleLoadedU(modulename: String ):  Boolean;
external 'IsModuleLoaded@{app}\psvince.dll stdcall uninstallonly' ;

;判断进程是否存在
function IsAppRunning(const FileName : string): Boolean;
var
    FSWbemLocator: Variant;
    FWMIService   : Variant;
    FWbemObjectSet: Variant;
begin
    Result := false;
    try
      FSWbemLocator := CreateOleObject('WBEMScripting.SWBEMLocator');
      FWMIService := FSWbemLocator.ConnectServer('', 'root\CIMV2', '', '');
      FWbemObjectSet := FWMIService.ExecQuery(Format('SELECT Name FROM Win32_Process Where Name="%s"',[FileName]));
      Result := (FWbemObjectSet.Count > 0);
      FWbemObjectSet := Unassigned;
      FWMIService := Unassigned;
      FSWbemLocator := Unassigned;
    except
      if (IsModuleLoaded(FileName)) then
        begin
          Result := false;
        end
      else
        begin
          Result := true;
        end
      end;
end;

;通过名称终结进程
procedure TaskKillProcessByName(AppName: String);
var
  WbemLocator : Variant;
  WMIService   : Variant;
  WbemObjectSet: Variant;
  WbemObject   : Variant;
begin;
  WbemLocator := CreateOleObject('WbemScripting.SWbemLocator');
  WMIService := WbemLocator.ConnectServer('localhost', 'root\CIMV2');
  WbemObjectSet := WMIService.ExecQuery('SELECT * FROM Win32_Process Where Name="' + AppName + '"');
  if not VarIsNull(WbemObjectSet) and (WbemObjectSet.Count > 0) then
  begin
    WbemObject := WbemObjectSet.ItemIndex(0);
    if not VarIsNull(WbemObject) then
    begin
      WbemObject.Terminate();
      WbemObject := Unassigned;
    end;
  end;
end;

;安装的时候判断进程是否存在，存在则先提示是否结束进程
function InitializeSetup(): Boolean;
begin
  Result := true;
  if  IsAppRunning('{#MyAppExeName}') then
  begin
    if MsgBox('安装程序检测到 {#MyAppName} 正在运行！'#13''#13'单击“是”按钮关闭程序并继续安装；'#13''#13'单击“否”按钮退出安装！', mbConfirmation, MB_YESNO) = IDYES then
    begin
      TaskKillProcessByName('{#MyAppExeName}');
      TaskKillProcessByName('{#MyAppExeName}');
      Result:= true;
    end
    else
      Result:= false;
  end;
end;

;卸载的时候判断进程是否存在，存在则先提示是否结束进程
function InitializeUninstall(): Boolean;
begin
    Result:= true;
    if  IsAppRunning('{#MyAppExeName}') then
    begin
      if MsgBox('卸载程序检测到 {#MyAppName} 正在运行！'#13''#13'单击“是”按钮关闭程序并继续卸载；'#13''#13'单击“否”按钮退出卸载！', mbConfirmation, MB_YESNO) = IDYES then
      begin
        TaskKillProcessByName('{#MyAppExeName}');
        TaskKillProcessByName('{#MyAppExeName}');
        Result:= true;
      end
      else
        Result:= false;
    end;
    // Unload the DLL, otherwise the dll psvince is not deleted
    UnloadDLL(ExpandConstant('{app}\psvince.dll'));
    DelTree(ExpandConstant('{app}'), True, True, True);
end;

```

## 二、通过isTask.dll进行检测
同样将其放到脚本所在目录即可

```
[Files]
Source: istask.dll; DestDir: {app}

[Code]
//安装前判断是否有进程正在运行，istask.dll文件与打包的exe文件一起
function RunTask(FileName: string; bFullpath: Boolean): Boolean;
external 'RunTask@files:ISTask.dll stdcall delayload';
function KillTask(ExeFileName: string): Integer;
external 'KillTask@files:ISTask.dll stdcall delayload';


function InitializeSetup(): Boolean;
begin
  Result:= true;
  if RunTask('{#MyAppExeName}', false) then
      begin
           if MsgBox('安装程序检测到 {#MyAppName} 或者 快乐付小助手 正在运行!'#13''#13'点击 "是" 按钮将停止进程然后继续安装;'#13''#13'点击"否" 按钮将终止安装程序!', mbConfirmation, MB_YESNO) = IDYES then
              begin
              KillTask('{#MyAppExeName}');
              Result:= true;
              end
           else
              Result:= false;
      end
  else
  Result:= true;
end;

function InitializeUninstall(): Boolean;
begin
  Result:= true;
  if RunTask('{#MyAppExeName}', false)then
      begin
           if MsgBox('安装程序检测到 {#MyAppName} 正在运行!'#13''#13'点击 "是" 按钮将停止进程然后继续安装;'#13''#13'点击"否" 按钮将终止安装程序!', mbConfirmation, MB_YESNO) = IDYES then
              begin
              KillTask('{#MyAppExeName}');
              Result:= true;
              end
           else
              Result:= false;
      end
  else
  Result:= true;
end;
```

## 三、资源
[psvince.dll](http://blog-res.oss-cn-hongkong.aliyuncs.com/resource/psvince.dll)
[istask.dll](http://blog-res.oss-cn-hongkong.aliyuncs.com/resource/istask.dll)