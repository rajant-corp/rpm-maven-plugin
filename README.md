# MojoHaus RPM Maven Plugin

This is a fork of [rpm-maven-plugin](http://www.mojohaus.org/rpm-maven-plugin/).
 
The `2.2.0-rajant` branch adds support for arbitrary `%global` statements in the generated spec file.

The need for this arose when bundling the JRE with a Java app.  RPM wanted to have define an external dependency on libjli.so.  This file is included in the JRE distribuution, but since 
the library is requirement of the "java" vm (as reported by ldd) RPM wanted the file to already exist on the system. 

One alternative would be to use `<autoRequires>false</autoRequires>`, but this ignores important JVM dependencies - like libc.  If the dependencies for the JVM were not defined 
the user could install the app on a old/unsupported OS.

To prevent this from occuring we needed to insert a few `%global` statements, like the following. The plugin did not provide a mechanism for defining these statements
```
%global _privatelibs librmi[.]so.*|libjli[.]so.*|libnio[.]so.*
%global __provides_exclude ^(%{_privatelibs})$
%global __requires_exclude ^(%{_privatelibs})$
```

POM example for bundled JRE 11 (as of JRE 11.0.11 - libraries may change in future version YMMV)
```
<configuration>
...
    <globalStatements>
        <globalStatement> 
           _privatelibs librmi[.]so.*|libjli[.]so.*|libnio[.]so.*|libjsig[.]so.*|libjimage[.]so.*|libfontmanager[.]so.*|libmanagement_ext[.]so.*|libinstrument[.]so.*|libunpack[.]so.*|libsctp[.]so.*|libjdwp[.]so.*|libjsound[.]so.*|libj2gss[.]so.*|libmanagement_agent[.]so.*|libharfbuzz[.]so.*|libawt[.]so.*|libextnet[.]so.*|libawt_headless[.]so.*|liblcms[.]so.*|libjsig[.]so.*|libjvm[.]so.*|libmlib_image[.]so.*|libj2pkcs11[.]so.*|libnet[.]so.*|libverify[.]so.*|libsplashscreen[.]so.*|libsunec[.]so.*|libmanagement[.]so.*|libj2pcsc[.]so.*|libawt_xawt[.]so.*|libjava[.]so.*|libprefs[.]so.*|libjavajpeg[.]so.*|libjawt[.]so.*|libdt_socket[.]so.*|libzip[.]so.*|libjaas[.]so.*
        </globalStatement>
        <globalStatement>
          __provides_exclude ^(%{_privatelibs})$
        </globalStatement>
        <globalStatement>
        __requires_exclude ^(%{_privatelibs})$
        </globalStatement>
    </globalStatements>
</configuration>
```
Caution should be taken when using global statements - there is NO VALIDATION - best to understand "rpm" - RTFM.

