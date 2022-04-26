<!--yml
category: 未分类
date: 2022-04-26 14:49:26
-->

# CTF中文件上传题目整理总结_xiaosec的博客-CSDN博客_文件上传ctf题目

> 来源：[https://blog.csdn.net/xiaotaode2012/article/details/77234535](https://blog.csdn.net/xiaotaode2012/article/details/77234535)

**0X00说在前面的话**

CTF中或多或少都有点文件上传的题目，而这个又是最好整理的，变化的方式不是很多（至少到目前为止我没有发现太多的姿势。。。。），随意就先整理这个吧。

 **0x01文件上传绕过的主要姿势有：**

A：基于前台JS的验证，这个 不要说就是firebug下修改一下JS文件就绕过了
B：基于文件后缀名的绕过，这里面的主要姿势有：利用后缀名大小写混用绕过、空格或者加点的方式绕过，还有对于PHP来说就是PHP3、PHP4、PHP5这种方式绕过，同时还可以考虑的是%00阶段绕过。这种绕过主要是利用了黑名单以及白名单的特点，去测试系统是采用白名单还是黑名单。
C：基于文件类型的检测：Content-type 
D：基于文件头部信息的过滤

 **0x02:考察结合点：**
白名单：控制目录配合解析漏洞 iis6.0: 1.asp/xxx.xxxx 1.asp;.xxx asa,cer,
Iis7.5/nginx<0.8 (php.cgi)  
Apache 1.php.rar
与黑名单：
只要不是黑名单内的类型均可

 **0x03文件包含结合的点主要是中间件的解析漏洞来利用：**
中间件的解析漏洞主要有：
A:IIS 6.0解析利用方法有两种
1.目录解析
/xx.asp/xx.jpg

2.文件解析
sp.asp;.jpg

第一种，在网站下建立文件夹的名字为 .asp、.asa 的文件夹，其目录内的任何扩展名的文件都被IIS当作asp文件来解析并执行。
例如创建目录 sp.asp，那么/sp.asp/1.jpg将被当作asp文件来执行。假设黑阔可以控制上传文件夹路径,就可以不管你上传后你的图片改不改名都能拿shell了。
第二种，在IIS6.0下，分号后面的不被解析，也就是说sp.asp;.jpg会被服务器看成是sp.asp
还有IIS6.0 默认的可执行文件除了asp还包含这三种
/sp.asa、/sp.cer、/sp.cdx

 **附录http Content-type类型表：http://tool.oschina.net/commons**

文件扩展名 Content-Type(Mime-Type) 文件扩展名 Content-Type(Mime-Type)
.*（ 二进制流，不知道下载文件类型） application/octet-stream .tif image/tiff
.001 application/x-001 .301 application/x-301
.323 text/h323 .906 application/x-906
.907 drawing/907 .a11 application/x-a11
.acp audio/x-mei-aac .ai application/postscript
.aif audio/aiff .aifc audio/aiff
.aiff audio/aiff .anv application/x-anv
.asa text/asa .asf video/x-ms-asf
.asp text/asp .asx video/x-ms-asf
.au audio/basic .avi video/avi
.awf application/vnd.adobe.workflow .biz text/xml
.bmp application/x-bmp .bot application/x-bot
.c4t application/x-c4t .c90 application/x-c90
.cal application/x-cals .cat application/vnd.ms-pki.seccat
.cdf application/x-netcdf .cdr application/x-cdr
.cel application/x-cel .cer application/x-x509-ca-cert
.cg4 application/x-g4 .cgm application/x-cgm
.cit application/x-cit .class java/*
.cml text/xml .cmp application/x-cmp
.cmx application/x-cmx .cot application/x-cot
.crl application/pkix-crl .crt application/x-x509-ca-cert
.csi application/x-csi .css text/css
.cut application/x-cut .dbf application/x-dbf
.dbm application/x-dbm .dbx application/x-dbx
.dcd text/xml .dcx application/x-dcx
.der application/x-x509-ca-cert .dgn application/x-dgn
.dib application/x-dib .dll application/x-msdownload
.doc application/msword .dot application/msword
.drw application/x-drw .dtd text/xml
.dwf Model/vnd.dwf .dwf application/x-dwf
.dwg application/x-dwg .dxb application/x-dxb
.dxf application/x-dxf .edn application/vnd.adobe.edn
.emf application/x-emf .eml message/rfc822
.ent text/xml .epi application/x-epi
.eps application/x-ps .eps application/postscript
.etd application/x-ebx .exe application/x-msdownload
.fax image/fax .fdf application/vnd.fdf
.fif application/fractals .fo text/xml
.frm application/x-frm .g4 application/x-g4
.gbr application/x-gbr . application/x-
.gif image/gif .gl2 application/x-gl2
.gp4 application/x-gp4 .hgl application/x-hgl
.hmr application/x-hmr .hpg application/x-hpgl
.hpl application/x-hpl .hqx application/mac-binhex40
.hrf application/x-hrf .hta application/hta
.htc text/x-component .htm text/html
.html text/html .htt text/webviewhtml
.htx text/html .icb application/x-icb
.ico image/x-icon .ico application/x-ico
.iff application/x-iff .ig4 application/x-g4
.igs application/x-igs .iii application/x-iphone
.img application/x-img .ins application/x-internet-signup
.isp application/x-internet-signup .IVF video/x-ivf
.java java/* .jfif image/jpeg
.jpe image/jpeg .jpe application/x-jpe
.jpeg image/jpeg .jpg image/jpeg
.jpg application/x-jpg .js application/x-javascript
.jsp text/html .la1 audio/x-liquid-file
.lar application/x-laplayer-reg .latex application/x-latex
.lavs audio/x-liquid-secure .lbm application/x-lbm
.lmsff audio/x-la-lms .ls application/x-javascript
.ltr application/x-ltr .m1v video/x-mpeg
.m2v video/x-mpeg .m3u audio/mpegurl
.m4e video/mpeg4 .mac application/x-mac
.man application/x-troff-man .math text/xml
.mdb application/msaccess .mdb application/x-mdb
.mfp application/x-shockwave-flash .mht message/rfc822
.mhtml message/rfc822 .mi application/x-mi
.mid audio/mid .midi audio/mid
.mil application/x-mil .mml text/xml
.mnd audio/x-musicnet-download .mns audio/x-musicnet-stream
.mocha application/x-javascript .movie video/x-sgi-movie
.mp1 audio/mp1 .mp2 audio/mp2
.mp2v video/mpeg .mp3 audio/mp3
.mp4 video/mpeg4 .mpa video/x-mpg
.mpd application/vnd.ms-project .mpe video/x-mpeg
.mpeg video/mpg .mpg video/mpg
.mpga audio/rn-mpeg .mpp application/vnd.ms-project
.mps video/x-mpeg .mpt application/vnd.ms-project
.mpv video/mpg .mpv2 video/mpeg
.mpw application/vnd.ms-project .mpx application/vnd.ms-project
.mtx text/xml .mxp application/x-mmxp
.net image/pnetvue .nrf application/x-nrf
.nws message/rfc822 .odc text/x-ms-odc
.out application/x-out .p10 application/pkcs10
.p12 application/x-pkcs12 .p7b application/x-pkcs7-certificates
.p7c application/pkcs7-mime .p7m application/pkcs7-mime
.p7r application/x-pkcs7-certreqresp .p7s application/pkcs7-signature
.pc5 application/x-pc5 .pci application/x-pci
.pcl application/x-pcl .pcx application/x-pcx
.pdf application/pdf .pdf application/pdf
.pdx application/vnd.adobe.pdx .pfx application/x-pkcs12
.pgl application/x-pgl .pic application/x-pic
.pko application/vnd.ms-pki.pko .pl application/x-perl
.plg text/html .pls audio/scpls
.plt application/x-plt .png image/png
.png application/x-png .pot application/vnd.ms-powerpoint
.ppa application/vnd.ms-powerpoint .ppm application/x-ppm
.pps application/vnd.ms-powerpoint .ppt application/vnd.ms-powerpoint
.ppt application/x-ppt .pr application/x-pr
.prf application/pics-rules .prn application/x-prn
.prt application/x-prt .ps application/x-ps
.ps application/postscript .ptn application/x-ptn
.pwz application/vnd.ms-powerpoint .r3t text/vnd.rn-realtext3d
.ra audio/vnd.rn-realaudio .ram audio/x-pn-realaudio
.ras application/x-ras .rat application/rat-file
.rdf text/xml .rec application/vnd.rn-recording
.red application/x-red .rgb application/x-rgb
.rjs application/vnd.rn-realsystem-rjs .rjt application/vnd.rn-realsystem-rjt
.rlc application/x-rlc .rle application/x-rle
.rm application/vnd.rn-realmedia .rmf application/vnd.adobe.rmf
.rmi audio/mid .rmj application/vnd.rn-realsystem-rmj
.rmm audio/x-pn-realaudio .rmp application/vnd.rn-rn_music_package
.rms application/vnd.rn-realmedia-secure .rmvb application/vnd.rn-realmedia-vbr
.rmx application/vnd.rn-realsystem-rmx .rnx application/vnd.rn-realplayer
.rp image/vnd.rn-realpix .rpm audio/x-pn-realaudio-plugin
.rsml application/vnd.rn-rsml .rt text/vnd.rn-realtext
.rtf application/msword .rtf application/x-rtf
.rv video/vnd.rn-realvideo .sam application/x-sam
.sat application/x-sat .sdp application/sdp
.sdw application/x-sdw .sit application/x-stuffit
.slb application/x-slb .sld application/x-sld
.slk drawing/x-slk .smi application/smil
.smil application/smil .smk application/x-smk
.snd audio/basic .sol text/plain
.sor text/plain .spc application/x-pkcs7-certificates
.spl application/futuresplash .spp text/xml
.ssm application/streamingmedia .sst application/vnd.ms-pki.certstore
.stl application/vnd.ms-pki.stl .stm text/html
.sty application/x-sty .svg text/xml
.swf application/x-shockwave-flash .tdf application/x-tdf
.tg4 application/x-tg4 .tga application/x-tga
.tif image/tiff .tif application/x-tif
.tiff image/tiff .tld text/xml
.top drawing/x-top .torrent application/x-bittorrent
.tsd text/xml .txt text/plain
.uin application/x-icq .uls text/iuls
.vcf text/x-vcard .vda application/x-vda
.vdx application/vnd.visio .vml text/xml
.vpg application/x-vpeg005 .vsd application/vnd.visio
.vsd application/x-vsd .vss application/vnd.visio
.vst application/vnd.visio .vst application/x-vst
.vsw application/vnd.visio .vsx application/vnd.visio
.vtx application/vnd.visio .vxml text/xml
.wav audio/wav .wax audio/x-ms-wax
.wb1 application/x-wb1 .wb2 application/x-wb2
.wb3 application/x-wb3 .wbmp image/vnd.wap.wbmp
.wiz application/msword .wk3 application/x-wk3
.wk4 application/x-wk4 .wkq application/x-wkq
.wks application/x-wks .wm video/x-ms-wm
.wma audio/x-ms-wma .wmd application/x-ms-wmd
.wmf application/x-wmf .wml text/vnd.wap.wml
.wmv video/x-ms-wmv .wmx video/x-ms-wmx
.wmz application/x-ms-wmz .wp6 application/x-wp6
.wpd application/x-wpd .wpg application/x-wpg
.wpl application/vnd.ms-wpl .wq1 application/x-wq1
.wr1 application/x-wr1 .wri application/x-wri
.wrk application/x-wrk .ws application/x-ws
.ws2 application/x-ws .wsc text/scriptlet
.wsdl text/xml .wvx video/x-ms-wvx
.xdp application/vnd.adobe.xdp .xdr text/xml
.xfd application/vnd.adobe.xfd .xfdf application/vnd.adobe.xfdf
.xhtml text/html .xls application/vnd.ms-excel
.xls application/x-xls .xlw application/x-xlw
.xml text/xml .xpl audio/scpls
.xq text/xml .xql text/xml
.xquery text/xml .xsd text/xml
.xsl text/xml .xslt text/xml
.xwd application/x-xwd .x_b application/x-x_b
.sis application/vnd.symbian.install .sisx application/vnd.symbian.install
.x_t application/x-x_t .ipa application/vnd.iphone
.apk application/vnd.android.package-archive .xap application/x-silverlight-app

**各种文件格式的头部信息：**
JPEG (jpg)，文件头：FFD8FF

PNG (png)，文件头：89504E47                       

GIF (gif)，文件头：47494638

TIFF (tif)，文件头：49492A00                       

Windows Bitmap (bmp)，文件头：424D

CAD (dwg)，文件头：41433130                       

Adobe Photoshop (psd)，文件头：38425053                       

Rich Text Format (rtf)，文件头：7B5C727466

XML (xml)，文件头：3C3F786D6C                       

HTML (html)，文件头：68746D6C3E                      

Email [thorough only]

(eml)，文件头：44656C69766572792D646174653A                      

Outlook Express (dbx)，文件头：CFAD12FEC5FD746F

Outlook (pst)，文件头：2142444E

MS Word/Excel (xls.or.doc)，文件头：D0CF11E0

MS Access (mdb)，文件头：5374616E64617264204A

WordPerfect (wpd)，文件头：FF575043

Postscript. (eps.or.ps)，文件头：252150532D41646F6265                       

Adobe Acrobat (pdf)，文件头：255044462D312E                       

Quicken (qdf)，文件头：AC9EBD8F                       

Windows Password (pwl)，文件头：E3828596

ZIP Archive (zip)，文件头：504B0304

RAR Archive (rar)，文件头：52617221

Wave (wav)，文件头：57415645

AVI (avi)，文件头：41564920

Real Audio (ram)，文件头：2E7261FD                     

Real Media (rm)，文件头：2E524D46

MPEG (mpg)，文件头：000001BA                       

MPEG (mpg)，文件头：000001B3

Quicktime (mov)，文件头：6D6F6F76                       

Windows Media (asf)，文件头：3026B2758E66CF11

MIDI (mid)，文件头：4D546864