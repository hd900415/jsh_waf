# waf
ʹ��Nginx+Luaʵ���Զ���WAF��Web application firewall��

##��Ȩ����
    ���زο����ճ���https://github.com/loveshell/ngx_lua_waf
    ���زο����ճ���https://github.com/unixhot/waf
####�����б�
1.	֧��IP�������ͺ��������ܣ�ֱ�ӽ���������IP���ʾܾ���
2.	֧��URL��������������Ҫ���˵�URL���ж��塣
3.	֧��User-Agent�Ĺ��ˣ�ƥ���Զ�������е���Ŀ��Ȼ����д�������403����
4.	֧��Cookie���ˣ�ƥ���Զ�������е���Ŀ��Ȼ����д�������403����
5.	֧��URL���ˣ�ƥ���Զ�������е���Ŀ������û������URL������Щ������403��
6.	֧��URL�������ˣ�ԭ��ͬ�ϡ�
7.	֧����־��¼�������оܾ��Ĳ�������¼����־��ȥ��

####WAFʵ��
   WAFһ�仰���������ǽ���HTTP����Э�����ģ�飩�������⣨����ģ�飩������ͬ�ķ�������������ģ�飩�������������̣���־ģ�飩��¼���������Ա����е�WAF��ʵ�������ģ��(����ģ�顢Э�����ģ�顢����ģ�顢����ģ�顢������ģ�飩��ɡ�

####Nginx + Lua����

����׼��
    [root@nginx-lua ~]# cd /usr/local/src
���ȣ�����Nginx��װ�ر���Nginx��PCRE�������
<pre>
[root@nginx-lua src]# wget http://nginx.org/download/nginx-1.9.4.tar.gz
[root@nginx-lua src]# wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.37.tar.gz
</pre>
��Σ����ص�ǰ���µ�luajit��ngx_devel_kit (NDK)���Լ����磨�£���д��lua-nginx-module
<pre>
  [root@nginx-lua src]# wget http://luajit.org/download/LuaJIT-2.0.4.tar.gz
  [root@nginx-lua src]# wget https://github.com/simpl/ngx_devel_kit/archive/v0.2.19.tar.gz
  [root@nginx-lua src]# wget https://github.com/openresty/lua-nginx-module/archive/v0.9.16.tar.gz
</pre>

��󣬴���Nginx���е���ͨ�û�
   [root@nginx-lua src]# useradd -s /sbin/nologin -M www

��ѹNDK��lua-nginx-module
<pre>
    [root@openstack-compute-node5 src]# tar zxvf v0.2.19 ��ѹ��Ϊngx_devel_kit-0.2.19
    [root@openstack-compute-node5 src]# tar zxvf v0.9.10��ѹ��Ϊlua-nginx-module-0.9.16
</pre>

��װLuaJIT
Luajit��Lua��ʱ��������
<pre>
  [root@openstack-compute-node5 src]# tar zxvf LuaJIT-2.0.3.tar.gz 
  [root@openstack-compute-node5 src]# cd LuaJIT-2.0.3
  [root@openstack-compute-node5 LuaJIT-2.0.3]# make && make install
</pre>

��װNginx������ģ��
<pre>
  [root@openstack-compute-node5 src]# tar zxvf nginx-1.9.4.tar.gz 
  [root@openstack-compute-node5 src]# cd nginx-1.9.4
  [root@openstack-compute-node5 nginx-1.9.4]# export LUAJIT_LIB=/usr/local/lib
  [root@openstack-compute-node5 nginx-1.9.4]# export LUAJIT_INC=/usr/local/include/luajit-2.0
  [root@openstack-compute-node5 nginx-1.9.4]# ./configure --prefix=/usr/local/nginx --user=www --group=www     --with-http_ssl_module --with-http_stub_status_module --with-file-aio --with-http_dav_module --add-module=../ngx_devel_kit-0.2.19/ --add-module=../lua-nginx-module-0.9.16/ --with-pcre=/usr/local/src/pcre-8.37 
  [root@openstack-compute-node5 nginx-1.5.12]# make -j2 && make install

  [root@openstack-compute-node5 ~]# ln -s /usr/local/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2
</pre>
����������������ӣ����ܳ��������쳣��
error while loading shared libraries: libluajit-5.1.so.2: cannot open shared object file: No such file or directory

#####���԰�װ
��װ��Ϻ�������Բ��԰�װ�ˣ��޸�nginx.conf ���ӵ�һ�����á�
<pre>
        location /hello {
                default_type 'text/plain';
                content_by_lua 'ngx.say("hello,lua")';
        }
    
[root@openstack-compute-node5 ~]# /usr/local/nginx-1.9.4/sbin/nginx �Ct
[root@openstack-compute-node5 ~]# /usr/local/nginx-1.9.4/sbin/nginx
</pre>

Ȼ�����http://xxx.xxx.xxx.xxx/hello���������hello,lua����ʾ��װ���,Ȼ��Ϳ��ԡ�
####WAF����

<pre>
#cd /usr/local/nginx/conf
#git clone https://github.com/tluolovembtan/jsh_waf


�޸�Nginx�������ļ��������������á�ע��·����ͬʱWAF��־Ĭ�ϴ����/tmp/����_waf.log
#WAF
    lua_shared_dict limit 50m;
    lua_package_path "/usr/local/openresty/nginx/conf/jsh_waf/?.lua";
    init_by_lua_file "/usr/local/openresty/nginx/conf/jsh_waf/init.lua";
    access_by_lua_file "/usr/local/openresty/nginx/conf/jsh_waf/waf.lua";

[root@openstack-compute-node5 ~]# /usr/local/nginx/sbin/nginx
</pre>
