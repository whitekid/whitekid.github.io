---
id: 1152
title: OpenStack은 정말 복잡한 프로젝트다..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1152
permalink: /archives/1152
dsq_thread_id:
  - 949199600
categories:
  - Uncategorized
tags:
  - 잡담
---
문득 얼마나 많은 패키지들이 OpenStack을 위해서 필요한지 단순하게 체크해밨다.

> $ apt-get install mysql-server rabbitmq-server \  
> keystone glance-api glance-registry \  
> nova-api nova-scheduler nova-novncproxy nova-cert nova-consoleauth \  
> cinder-api cinder-scheduler cinder-volume \  
> nova-compute \  
> quantum-server quantum-plugin-openvswitch quantum-plugin-openvswitch-agent quantum-l3-agent quantum-dhcp-agent \  
> openstack-dashboard

단순하게 전체 구성하는 패키지를 설치하는 명령을 내리면...

    Reading package lists... Done  
    Building dependency tree  
    Reading state information... Done  
    The following extra packages will be installed:  
    apache2 apache2-mpm-worker apache2-utils apache2.2-bin apache2.2-common augeas-lenses binutils bridge-utils cgroup-lite cinder-common cpp cpp-4.6 cpu-checker curl dbconfig-common dkms dnsmasq-base dnsmasq-utils ebtables  
    erlang-asn1 erlang-base erlang-corba erlang-crypto erlang-dev erlang-diameter erlang-docbuilder erlang-edoc erlang-erl-docgen erlang-eunit erlang-ic erlang-inets erlang-inviso erlang-mnesia erlang-nox erlang-odbc  
    erlang-os-mon erlang-parsetools erlang-percept erlang-public-key erlang-runtime-tools erlang-snmp erlang-ssh erlang-ssl erlang-syntax-tools erlang-tools erlang-webtool erlang-xmerl fakeroot gawk gcc gcc-4.6 git git-man  
    glance-common kpartx kvm kvm-ipxe libaio1 libapache2-mod-wsgi libapparmor1 libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libasound2 libasyncns0 libaugeas0 libavahi-client3 libavahi-common-data libavahi-common3  
    libc-dev-bin libc6-dev libcaca0 libconfig-general-perl libcurl3 libdbd-mysql-perl libdbi-perl liberror-perl libevent-2.0-5 libflac8 libgmp10 libgomp1 libhtml-template-perl libibverbs1 libjs-jquery libjs-sphinxdoc  
    libjs-underscore libjson0 libltdl7 libmpc2 libmpfr4 libmysqlclient18 libnet-daemon-perl libnetcf1 libnetfilter-conntrack3 libnl-route-3-200 libnspr4 libnss3 libnuma1 libodbc1 libogg0 libplrpc-perl libpulse0 libquadmath0  
    librados2 librbd1 librdmacm1 libsctp1 libsdl1.2debian libsgutils2-2 libsigsegv2 libsndfile1 libtidy-0.99-0 libvirt-bin libvirt0 libvorbis0a libvorbisenc2 libxenstore3.0 libxml2-utils libxslt1.1 libyajl1 libyaml-0-2  
    linux-libc-dev lksctp-tools make manpages-dev memcached msr-tools mysql-client-5.5 mysql-client-core-5.5 mysql-common mysql-server-5.5 mysql-server-core-5.5 nova-common nova-compute-kvm open-iscsi open-iscsi-utils  
    openstack-dashboard-ubuntu-theme openvswitch-common openvswitch-datapath-dkms openvswitch-switch patch python-amqplib python-anyjson python-appconf python-boto python-carrot python-cheetah python-cinder python-cinderclient  
    python-cliff python-cloudfiles python-cmd2 python-compressor python-configobj python-crypto python-daemon python-dateutil python-decorator python-dingus python-django python-django-horizon python-eventlet python-feedparser  
    python-formencode python-gevent python-gflags python-glance python-glanceclient python-greenlet python-httplib2 python-iso8601 python-jsonschema python-keyring python-keystone python-keystoneclient python-kombu python-ldap  
    python-libvirt python-libxml2 python-lockfile python-lxml python-m2crypto python-memcache python-migrate python-mysqldb python-netaddr python-nose python-nova python-novaclient python-openid python-openssl  
    python-openstack-auth python-pam python-paramiko python-passlib python-paste python-pastedeploy python-pastescript python-pkg-resources python-prettytable python-pycurl python-pyparsing python-pyudev python-quantum  
    python-quantumclient python-requests python-routes python-scgi python-setuptools python-setuptools-git python-simplejson python-sqlalchemy python-sqlalchemy-ext python-stompy python-suds python-swiftclient python-tempita  
    python-tz python-utidylib python-warlock python-webob python-xattr python-yaml qemu-common qemu-kvm qemu-utils quantum-common seabios sg3-utils ssl-cert tgt vgabios vlan  
    Suggested packages:  
    www-browser apache2-doc apache2-suexec apache2-suexec-custom augeas-doc binutils-doc python-ceph cpp-doc gcc-4.6-locales virtual-mysql-client mysql-client postgresql-client erlang erlang-manpages erlang-doc xsltproc fop  
    erlang-ic-java erlang-observer gcc-multilib autoconf automake1.9 libtool flex bison gdb gcc-doc gcc-4.6-multilib libmudflap0-4.6-dev gcc-4.6-doc libgcc1-dbg libgomp1-dbg libquadmath0-dbg libmudflap0-dbg binutils-gold  
    git-daemon-run git-daemon-sysvinit git-doc git-el git-arch git-cvs git-svn git-email git-gui gitk gitweb libasound2-plugins libasound2-python augeas-tools glibc-doc libipc-sharedcache-perl javascript-common libmyodbc  
    odbc-postgresql tdsodbc unixodbc-bin pulseaudio policykit-1 pm-utils radvd make-doc libcache-memcached-perl libmemcached libterm-readkey-perl tinyca mailx novnc ethtool diffutils-doc python-amqplib-doc python-markdown  
    python-pygments python-crypto-dbg python-crypto-doc python-psycopg2 python-psycopg python-flup python-sqlite geoip-database-contrib python-egenix-mxdatetime python-dns python-gevent-doc python-gevent-dbg python-greenlet-dbg  
    python-greenlet-dev python-greenlet-doc python-couchdb python-kombu-doc python-pymongo python-ldap-doc python-lxml-dbg python-mysqldb-dbg ipython python-coverage python-nose-doc python-openssl-doc python-openssl-dbg  
    python-pam-dbg python-pastewebkit libapache2-mod-python libapache2-mod-scgi python-pgsql libjs-mochikit python-cherrypy python-distribute python-distribute-doc libcurl4-gnutls-dev python-pycurl-dbg python-gobject python-qt4  
    python-pyside.qtcore python-sqlalchemy-doc python-kinterbasdb python-pymssql mol-drivers-macosx openbios-sparc ubuntu-vm-builder uml-utilities openssl-blacklist  
    The following NEW packages will be installed:  
    apache2 apache2-mpm-worker apache2-utils apache2.2-bin apache2.2-common augeas-lenses binutils bridge-utils cgroup-lite cinder-api cinder-common cinder-scheduler cinder-volume cpp cpp-4.6 cpu-checker curl dbconfig-common dkms  
    dnsmasq-base dnsmasq-utils ebtables erlang-asn1 erlang-base erlang-corba erlang-crypto erlang-dev erlang-diameter erlang-docbuilder erlang-edoc erlang-erl-docgen erlang-eunit erlang-ic erlang-inets erlang-inviso erlang-mnesia  
    erlang-nox erlang-odbc erlang-os-mon erlang-parsetools erlang-percept erlang-public-key erlang-runtime-tools erlang-snmp erlang-ssh erlang-ssl erlang-syntax-tools erlang-tools erlang-webtool erlang-xmerl fakeroot gawk gcc  
    gcc-4.6 git git-man glance-api glance-common glance-registry keystone kpartx kvm kvm-ipxe libaio1 libapache2-mod-wsgi libapparmor1 libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libasound2 libasyncns0 libaugeas0  
    libavahi-client3 libavahi-common-data libavahi-common3 libc-dev-bin libc6-dev libcaca0 libconfig-general-perl libcurl3 libdbd-mysql-perl libdbi-perl liberror-perl libevent-2.0-5 libflac8 libgmp10 libgomp1  
    libhtml-template-perl libibverbs1 libjs-jquery libjs-sphinxdoc libjs-underscore libjson0 libltdl7 libmpc2 libmpfr4 libmysqlclient18 libnet-daemon-perl libnetcf1 libnetfilter-conntrack3 libnl-route-3-200 libnspr4 libnss3  
    libnuma1 libodbc1 libogg0 libplrpc-perl libpulse0 libquadmath0 librados2 librbd1 librdmacm1 libsctp1 libsdl1.2debian libsgutils2-2 libsigsegv2 libsndfile1 libtidy-0.99-0 libvirt-bin libvirt0 libvorbis0a libvorbisenc2  
    libxenstore3.0 libxml2-utils libxslt1.1 libyajl1 libyaml-0-2 linux-libc-dev lksctp-tools make manpages-dev memcached msr-tools mysql-client-5.5 mysql-client-core-5.5 mysql-common mysql-server mysql-server-5.5  
    mysql-server-core-5.5 nova-api nova-cert nova-common nova-compute nova-compute-kvm nova-consoleauth nova-novncproxy nova-scheduler open-iscsi open-iscsi-utils openstack-dashboard openstack-dashboard-ubuntu-theme  
    openvswitch-common openvswitch-datapath-dkms openvswitch-switch patch python-amqplib python-anyjson python-appconf python-boto python-carrot python-cheetah python-cinder python-cinderclient python-cliff python-cloudfiles  
    python-cmd2 python-compressor python-configobj python-crypto python-daemon python-dateutil python-decorator python-dingus python-django python-django-horizon python-eventlet python-feedparser python-formencode python-gevent  
    python-gflags python-glance python-glanceclient python-greenlet python-httplib2 python-iso8601 python-jsonschema python-keyring python-keystone python-keystoneclient python-kombu python-ldap python-libvirt python-libxml2  
    python-lockfile python-lxml python-m2crypto python-memcache python-migrate python-mysqldb python-netaddr python-nose python-nova python-novaclient python-openid python-openssl python-openstack-auth python-pam python-paramiko  
    python-passlib python-paste python-pastedeploy python-pastescript python-pkg-resources python-prettytable python-pycurl python-pyparsing python-pyudev python-quantum python-quantumclient python-requests python-routes  
    python-scgi python-setuptools python-setuptools-git python-simplejson python-sqlalchemy python-sqlalchemy-ext python-stompy python-suds python-swiftclient python-tempita python-tz python-utidylib python-warlock python-webob  
    python-xattr python-yaml qemu-common qemu-kvm qemu-utils quantum-common quantum-dhcp-agent quantum-l3-agent quantum-plugin-openvswitch quantum-plugin-openvswitch-agent quantum-server rabbitmq-server seabios sg3-utils ssl-cert  
    tgt vgabios vlan  
    0 upgraded, 254 newly installed, 0 to remove and 0 not upgraded.  
    Need to get 124 MB of archives.  
    After this operation, 422 MB of additional disk space will be used.  
    Do you want to continue [Y/n]?  

254개의 패키지가 필요하다. 물론 모든 full stack을 알아야할 필요는 없겠지만, 어느 정도 그들이 어떤 역할을 하는지는 알아 두는 것이 전체 시스템을 이해하는데 도움이 많이 되겠죠.

완전히 기술 분야가 다른 리눅스 네트워킹부터~ django까지... 후훗... 질릴정도가?