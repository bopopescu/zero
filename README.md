# zero by bralike
#a project of openstack nova  
#nova组件的rpm包修改与制作以及原理讲解
#有关USB透传访问控制的底层代码修改 位于包中nova/virt/libvirt下，主要是为了改变libvirt.xml的结构
#关于xml文件的理解可以阅读书籍《王者归来》或参考本人博客http://www.cnblogs.com/cjy15639731813/p/5686571.html
#
#nova源码下载 http://releases.openstack.org/teams/nova.html
#openstack rpm包下载（网络源地址）https://repos.fedorapeople.org/repos/openstack/
#以下是具体步骤

预装好的openstack代码一般都在 /usr/lib/python*.*/site-packages/中 如果直接在此处改源码是无效的，那么，应该怎么办？

1、准备源码包 python-nova-2015.1.0-23.el7.noarch.rpm  此处位于SRPM中
2、将源码解压但不安装
    rpm2cpio dst.rpm | cpio -div (最好创建一个空文件夹并解压)
3、将解压内容拷贝至SOURCES中
    其中将openstack-nova.spec拷贝至SPEC下
4、执行rpmbuild -bp dst.spec 即从SPEC文件的%prep处开始建立（解压源码包并打补丁）
5、此时解压的源码包会在BUILD中生成源码文件夹：nova-2015.1.0
6、将nova-2015.1.0再拷贝一份
    cp -r nova-2015.1.0 nova-2015.1.0.org
7、这样就可以进入nova-2015.1.0改代码了。代码改动具体参照http://www.cnblogs.com/cjy15639731813/p/5686571.html中关于USB重定向的描述
8、改完后
    diff -arUN nova-2015.1.0 nova-2015.1.0.org >>nova-2015.1.0-xinda.patch
9、将nova-2015.1.0-xinda.patch拷贝至SOUCE并遵循文件夹内所有的patch格式修改名字0105-nova-2015.1.0-xinda.patch
10、编辑spec文件 在原有patch后加上一行 
    Patch0105: 0105-nova-2016-1.0-xinda.patch
    同时在%prep后定义：%Patch0105 patch p1 #此处p1指的是目录级别  p0为在当前目录打补丁 p1是一级 p2是二级
11、rpmbuild -ba dst.spec

#这样新的改动过的rpm包就做好了
