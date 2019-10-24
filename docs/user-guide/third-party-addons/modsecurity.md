# ModSecurity Web Application Firewall

ModSecurity是Trustwave的SpiderLabs开发的，用于Apache，IIS和Nginx的开源，跨平台Web应用程序防火墙(WAF)引擎。 
它具有强大的基于事件的编程语言，可提供针对Web应用程序的各种攻击的防护，并允许HTTP流量监视，
日志记录和实时分析-[https://www.modsecurity.org](https://www.modsecurity.org)

[ModSecurity-nginx](https://github.com/SpiderLabs/ModSecurity-nginx)连接器是NGINX与libmodsecurity(ModSecurity v3)之间的连接点。

默认的ModSecurity配置文件位于/etc/nginx/modsecurity/modsecurity.conf中。 这是此目录中的唯一文件，其中包含默认的建议配置。 使用 volume，我们可以使用所需的配置替换该文件。
要启用ModSecurity功能，我们需要在配置configmap中指定`enable-modsecurity:"true"。

>__Note:__ 默认配置仅使用检测，因为这样可以最大程度地减少安装后中断的机会。
           由于设置的值[SecAuditLogType = Concurrent](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual-(v2.x)#secauditlogtype)，ModSecurity日志存储在目录内的多个文件中 /var /log /audit。
           SecAuditLogType中的默认"Serial"值可能会影响性能。

OWASP ModSecurity核心规则集(CRS)是一组与ModSecurity或兼容的Web应用程序防火墙一起使用的通用攻击检测规则。 CRS旨在保护Web应用程序免受各种攻击，包括OWASP十大攻击，同时将虚假警报的发生率降至最低。
目录`/etc /nginx /owasp-modsecurity-crs`包含[owasp-modsecurity-crs存储库](https://github.com/SpiderLabs/owasp-modsecurity-crs)。
使用`enable-owasp-modsecurity-crs:"true"`，我们可以使用规则。
