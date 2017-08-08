Config selinux for server with socket at android 
1.MODIFY: system/core/rootdir/init.rc
service MyCustomeServer /system/bin/MyCustomeServer
    class main
    socket MyCustomeServer stream 600 system system
    
2.MODIFY: device/qcom/sepolicy/common/file_contexts
/system/bin/MyCustomeServer                  	u:object_r:MyCustomeServer_exec:s0

3.ADD: device/qcom/sepolicy/common/MyCustomeServer.te
type MyCustomeServer, domain, domain_deprecated;
type MyCustomeServer_exec, exec_type, file_type;

#Allow for transition from init domain to MyCustomeServer 
init_daemon_domain(MyCustomeServer)

#Allow MyCustomeServer  to use Binder IPC
binder_use(MyCustomeServer)

allow init socket_device:sock_file { create read write unlink setattr };

4.make bootimage
5.fastboot flash boot boot.img
6.fastboot reboot
7.adb shell restorecon /system/bin/MyCustomServer
8.adb reboot
9.check modification result
