***在 Android 6.0 之前，您可以合理地认为，如果应用能运行，它就已经获得在应用清单中声明的全部权限。***


权限分为三种:
- 安装时权限
- 运行时权限
- 特殊权限

## 安装时权限
在AndroidManifest.xml中声明:
``` xml
<manifest ...>
    <uses-permission android:name="android.permission.CAMERA"/>
    <application ...>
        ...
    </application>
</manifest>
```

## 运行时权限
***如果您确定您的应用需要声明和请求运行时权限，请完成以下步骤：***
```
1 在应用的清单文件中，声明应用可能需要请求的权限。
2 设计应用的用户体验，使应用中的特定操作与特定运行时权限相关联。告知用户哪些操作可能会要求他们向您的应用授予访问其私人数据的权限。
3 等待用户调用应用中需要访问特定用户私人数据的任务或操作。届时，您的应用可以请求访问相应数据所需的运行时权限。
4 检查用户是否已授予应用所需的运行时权限。如果已授权，那么您的应用可以访问用户私人数据。如果没有，请继续执行下一步。
每次执行需要该权限的操作时，您都必须检查自己是否具有该权限。

5 检查您的应用是否应向用户显示理由，说明您的应用需要用户授予特定运行时权限的原因。如果系统确定您的应用不应显示理由，请继续直接执行下一步，无需显示界面元素。
不过，如果系统确定您的应用应该显示一个理由，请在界面元素中向用户显示理由，明确说明您的应用试图访问哪些数据，以及应用获得运行时权限后可为用户提供哪些好处。用户确认理由后，请继续执行下一步。

6 请求您的应用访问用户私人数据所需的运行时权限。系统会显示运行时权限提示，例如权限概览页面上显示的提示。

7 检查用户的响应，他们可能会选择同意或拒绝授予运行时权限。

8 如果用户向您的应用授予相应权限，您就可以访问用户私人数据。如果用户拒绝授予该权限，请适当降低应用体验，使应用在未获得受该权限保护的信息时也能向用户提供功能
````

***从实际代码角度:***

1 确定应用是否已获得权限
```
如需检查用户是否已向您的应用授予特定权限，请将该权限传入 ContextCompat.checkSelfPermission() 方法。根据您的应用是否具有相应权限，此方法会返回 PERMISSION_GRANTED 或 PERMISSION_DENIED。
```

2 说明您的应用为何需要获取权限
```
系统在您调用 requestPermissions() 时显示的权限对话框将说明应用需要哪些权限，但不会解释为何需要这些权限。在某些情况下，用户可能会感到困惑。因此，最好在调用 requestPermissions() 之前向用户解释应用需要相应权限的原因
```

## 特殊权限
特殊权限旨在限制访问尤其敏感或与用户隐私没有直接关系的系统资源。这些权限不同于安装时权限和运行时权限;
- 设定精确的闹钟。
- 在其他应用前方显示和绘图。
- 访问所有存储数据。

*声明特殊权限的应用会显示在系统设置中的特殊应用权限页面内。如需向应用授予特殊权限，用户必须转到此页面：设置 > 应用 > 特殊应用权限。*

***与运行时权限不同，用户必须从系统设置中的特殊应用权限页面授予特殊权限。应用可以使用 intent 将用户转到该页面，这会暂停应用，并启动相应的设置页面，以便用户授予指定的特殊权限。用户返回到应用后，应用可以在 onResume() 函数中检查是否已获得相应权限。
以下示例代码展示了如何请求用户授予 SCHEDULE_EXACT_ALARMS 特殊权限：***
 ```kotlin
val alarmManager = getSystemService<AlarmManager>()!!
when {
   // if permission is granted, proceed with scheduling exact alarms…
   alarmManager.canScheduleExactAlarms() -> {
       alarmManager.setExact(...)
   }
   else -> {
       // ask users to grant the permission in the corresponding settings page
       startActivity(Intent(ACTION_REQUEST_SCHEDULE_EXACT_ALARM))
   }
}

 ```
 在 onResume() 中检查权限和处理用户决定的示例代码：
 ``` kotlin
override fun onResume() {
   // ...

   if (alarmManager.canScheduleExactAlarms()) {
       // proceed with the action (setting exact alarms)
       alarmManager.setExact(...)
   }
   else {
       // permission not yet approved. Display user notice and gracefully degrade
       your app experience.
       alarmManager.setWindow(...)
   }
}
 ```

 ***每项权限都有自己的检查方法
特殊权限的运作方式与运行时权限不同。因此，请参阅权限 API 参考文档页面，并针对每项特殊权限使用自定义权限检查函数。例如，使用 AlarmManager#canScheduleExactAlarms() 检查是否有 SCHEDULE_EXACT_ALARMS 权限，以及使用 Environment#isExternalStorageManager() 检查是否有 MANAGE_EXTERNAL_STORAGE 权限。***


*(待尝试)*
使用 adb 工具从命令行管理权限：
- 按组列出权限和状态：

$ adb shell pm list permissions -d -g
- 授予或撤消一项或多项权限：

$ adb shell pm [grant|revoke] <permission-name> ...

官方链接:https://developer.android.com/guide/topics/permissions/overview?hl=zh-cn