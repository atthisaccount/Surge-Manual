# Profile

The core functionality of Surge is controlled by the profile. Basically all content of the profile can be adjusted by the user interface. But some experimental features may not have the view to configure yet. When you encounter some special requirements, you might need to edit the profile manually to achieve it.

### Profile content

The format of the profile follows the format of the INI file, with [Section] segments, which are used to divide the different paragraphs and separate the settings.

The configuration lines of each section have their own specific syntax, such as [General], [Replica], and [MITM] sections are simply of the form key = value

```
[General]
key = value
```

In these paragraphs, the order of the configuration lines has no effect. However, in paragraphs such as [Rule], the order of the configuration lines up and down is very important.


### Classification of configuration files

Configuration files are divided into three categories.
1. Normal profile: created manually or used by default.
2. Managed profile: usually provided by the enterprise administrator or service provider. Managed profile cannot be modified locally because they can be updated remotely. If you want to make changes, you should first create a copy to transfer it to a normal configuration.
3. Enterprise profile: Enterprise version only, cannot be modified or viewed, and cannot be copied.


### Detached Profile Section

To meet the complexity of various usage scenarios, Surge Mac 4.1.0 Beta / Surge iOS Beta 1903 starts to support separation of one section of the profile into another file.

For example.

Main.conf
```
[Proxy]
#!include Proxy.dconf
```

The other file referenced therein must contain the [] declaration of the corresponding section. Thus, the file can be either a file containing only some of the sections (one or more), or a complete configuration.

Proxy.dconf
```
[Proxy]
ProxyA = http, 1.2.3.4, 80
```

Using this function, you can.
1. Reference the [Proxy], [Proxy Group], [Rule] paragraphs of a managed configuration and write other paragraphs yourself. This allows you to enjoy proxy-related content updates of the managed configuration without affecting other features adjusted through the UI.
2. Share the content of certain sections among multiple configurations. For example, when using Surge on both iOS and macOS, the contents of the [Proxy], [Proxy Group], [Rule], etc. sections are often the same, but the contents of [General] may be very different. You can create two configurations, iOS.conf and macOS.conf, and place the duplicate sections in another file.

```
[Proxy]
#!include Forwarding.dconf

[Proxy Group]
#!include Forwarding.dconf

[Rule]
#!include Forwarding.dconf
```

This way, when adjusting the [General] section on iOS, it does not affect macOS and avoids the hassle of maintaining two sets of proxy configurations. It also does not interfere at all with the configuration using the UI.

Some additional notes.
- After modifying the configuration via the UI, the configuration is written to the corresponding detached configuration segment file according to the include statement. If the file contains other paragraphs that are not used, the write will only modify the paragraphs in question.
- If a managed configuration is referenced, the configuration associated with that segment cannot be edited, but does not affect the adjustment of other segments.
- To complement this feature, all installed managed configurations will be checked for updates periodically in the new version of Surge Mac, whereas in the old version only the currently active configuration was checked for updates.
- The filename suffix is not required, if it is a complete configuration you can continue to use the conf suffix, if it is not a complete configuration it is recommended to use another suffix to avoid being displayed in the configuration list.

### Modules

The Detached Profile Section feature is used to split a single profile into multiple files, while modules are patches to the profile, each module file is used to fine tune various parts of the profile to achieve a specific task.

Modules can be:
- Flexibility to turn on and off.
- Tuning of multiple segments within the same file.
- Installed and kept up to date via URL.

However
- Module can not adjust the content of [Proxy], [Proxy Group], [Rule] sections.
- The module cannot adjust the CA certificate of MITM.
- The settings of the module override the main profile, so they cannot be adjusted via the UI.

The module description is available at: https://manual.nssurge.com/others/module.html


### Comment

Surge profile supports comment line, starts with '#', ';' and '//'. Comment inline will also be supported with separation of '//'. 

