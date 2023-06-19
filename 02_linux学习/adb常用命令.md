# 调试相关

## adb 服务
- 开启服务: `adb start-server`
- 关闭服务: `adb kill-server`

## 设备
- 列出设备及其id: `adb devices`
- 使用特定设备操作: `adb -s device_id`
  例如：`adb -s 47db0de20304 shell`

  ## 连接设备
  adb connect ip:端口号
  ## 断开设备
  adb disconnect ip:断开号

## 重启
- 正常重启: `adb reboot`
- 重启到 bootloader (刷机模式): `adb reboot bootloader`
- 重启到 recovery (恢复模式): `adb reboot recovery`

## Monkey 测试
- 随机测试: `adb shell monkey -p your.package.name 1000`
- script文件测试: `adb shell monkey -f /sdcard/xxx.script`

## 查看进程
- 列出进程列表及其pid: `adb shell ps`
- 杀死指定pid的进程: `adb shell kill pid`
- 查看指定进程信息: `adb shell ps -x pid`

## 查看 service
- `adb shell service list`

## 查看系统当前内存使用情况
- `adb shell cat /proc/meminfo`
- 查看指定包名应用内存使用情况: `adb shell dumpsys meminfo package`

## Activity 调试
- 启动应用: `adb shell am start -n your.package.name/your.package.name-activity`
- 停止应用: `adb shell am force-stop package`
- 查看当前 Activity 名称: `adb shell dumpsys activity | findstr "mFocusedActivity"`

# 设备应用管理
（这里推荐一个Android Stuido 插件：ADB Idea）

## 安装apk
- 安装: `adb install xxx.apk`
- 覆盖安装(保留缓存和数据): `adb install -r xxx.apk`

## 卸载应用
- 卸载: `adb uninstall package`
- 卸载时保留数据和缓存目录: `adb uninstall -k package`

## 查看设备应用
- 查看设备所有应用包名: `adb shell pm list packages`
- 列出指定包名对应的apk路径: `adb shell pm path your.package.name`
- 清空指定包名对应的应用的数据和缓存文件: `adb shell pm clear your.package.name`

# 文件管理

## 将电脑文件复制到设备sd卡上
- `adb push 电脑文件路径 /sdcard`

## 设备文件复制到电脑上
- `adb pull /sdcard/text.txt 电脑文件路径`

## 删除
- `adb shell rm /sdcard/text.txt`
- `-f` 强制删除文件不需要确认
- `-r` 递归删除文件夹内文件
- `-i` 删除文件前需要确认

## 创建目录
- `adb shell mkdir -p /sdcard/temp/test/` (指定 `-p` 递归创建目录)

## 创建文件
- `adb shell touch /sdcard/text.txt`

## 复制文件
- `adb shell cp /sdcard/text.txt /sdcard/test/`

## 移动文件
- `adb shell mv /sdcard/1.txt /sdcard/2.text`

## 其他一些命令
1. 申请 toot 权限
 `adb shell
su`

2. 查看手机上所有的包名
`adb shell
cd data/data ; ls`

3. Linux 多个命令行一起执行可以用 “;” 或者 “&&” 进行分割
`cd data/data ; ls`

4. adb shell 查看文件后退出
`exit 或者 Ctrl + D`

adb 官方链接
https://developer.android.google.cn