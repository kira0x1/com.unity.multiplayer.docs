---
id: migratingfrommlapi
title: Updating to the Unity Package
sidebar_label: Updating to the Unity Package
description: Learn how to upgrade your current MLAPI installation to the Unity MLAPI package.
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

:::important
This upgrade guide targets project using the MLAPI version which was installed by the MLAPI installer or by cloning the source code from Github.
If your project uses a Package Manager version of MLAPI then this guide is not relevant.
:::

The aqcuisiton of MLAPI has been a unique event in Unity history. In our efforts to integrate MLAPI into the Unity ecosystem, we are providing it in a Unity package.

What this means for you is:
- MLAPI will be easily accessible from the Unity Editor in the Package Manager window.
- Updating MLAPI in the future will work directly from the package manager.
- You will have full access to the MLAPI source in your project making it easier to debug and inspect MLAPI.
- Upgrading from the last MLAPI version to the new package version is a bit complicated. We are sorry for that.

## Backup your project

:::important Required
This step is **required**: Upgrading to the package version of MLAPI can cause issues with your current project. The new version modifies files and locations that will be drastically different than previous MLAPI versions.
:::

Backup your project using the following recommended methods:

* Create a copy of your entire project folder.
* Use source control software, like Git. 

:::bestpractice
We recommend using both methods to backup your project. This gives you a copied project and tracking through committed changes and history.
:::

##  Upgrade to Unity MLAPI

Manually upgrading from the `.dll` version installed by MLAPI to the new package version breaks all MLAPI component references in your scenes and prefabs. This is the case because MLAPI references components using GUIDs, which work differently for packages than for `.dll`s. 

To smoothly upgrade you to the new version of MLAPI, we have created an upgrade tool.

To start upgrading, add the upgrade tool to your project by using the **Add package from git URL..** option in the Package Manager window. Use the following URL: 

`https://github.com/Unity-Technologies/mlapi-community-contributions.git?path=/com.unity.multiplayer.mlapi-patcher`


After installing the patcher package you are good to go. Follow the following steps to upgrade.

## 1.**Install the MLAPI Package**
Follow the [installation guide](TODO how can I easily link to something telling the user how to install MLApI?) for installing the package version of MLAPI.

After installing the package, you will have error messages in the console, which is expected behavior. MLAPI uses and includes numerous new types, about double of your existing project's types.

## 2.**Updating Script References**

Open the MLAPI patcher window by selecting **Window** > **MLAPI Patcher** in the menu bar. The patcher will ask you whether you are using the *Installer* version of MLAPI or the *Source* version. 

Previously there were two major ways to use MLAPI in projects. You could either download a release version of MLAPI using the MLAPI installer or manually copy the source files into your project.

In the Patcher window, select *Installer** or *Source** button:

<Tabs
  className="unique-tabs"
  defaultValue="tab1"
  values={[
    {label: 'Upgrading from installer version.', value: 'tab1'},
    {label: 'Upgrading from source version.', value: 'tab2'},
  ]}>

<TabItem value="tab1">

1. Select **Installer**.
1. Select **Update Script References**.

</TabItem>
<TabItem value="tab2">

1. Select **Source**. 
1. The window prompts you to link a MLAPI source directory. 
1. Take the directory in your project containing the MLAPI source and drop it into the field. 
1. Select **Update Script References**.

</TabItem>
</Tabs>

After you complete the **Update Script References** process of the patcher, the MLAPI components on your `Prefabs` and `GameObject`s should have been updated to their new names. You can verify by checking if the `NetworkingManager` component is now called `NetworkManager`.

Most likely at this point you will get a warning telling you that the script cannot be loaded. No need to panic, this is expected and your serialized data won't be lost. As soon as you finish up migrating and clean up all error messages your data will appear again.

 ![NetworkManager component after upgrading](/img/upgrade-guide/networkmanager-component.png)


There is also a **Replace Type Names** button in the Patcher window. This step is optional. It automatically renames old type names in your scripts to the API changes made in Unity MLAPI, saving you some time to manually rename it. It performs a simple global replace of some of the type names. You may want to manually do this process instead if you want more control over changes.

## 3.**Remove older MLAPI versions**

Remove all the folders containing the existing non-package version of MLAPI from your project. Usually this means removing the `Assets/MLAPI` and `Assets/Editor/MLAPI` folders from the project.

## 4.**Upgrade your code to the new MLAPI APIs**

With our latest release we introduced a number of breaking API changes. With the move to Unity Packages, we took this chance to clean up the code base and provide an API that is easier to understand and use. We understand that upgrading to a new version with many breaking changes can be frustrating, and we are sorry for that.

### Review changes and release notes
See the [MLAPI Release Notes](../release-notes/release-0-1-0.md) for a list of all new features, refactored name changes, issue fixes, and more.

### Upgrade RPCs

The way RPCs are invoked has changed with this version of MLAPI. Please read our new [documentation about RPCs](TODO link?) and replace your existing RPCs with the new system.

### Serialization

We replaced the old `IBitSerializable` interface with a new `INetworkSerializable` interface. The interface works a bit different, check our [docs page] (TODO link doesn't exist yet?)

### SyncVars
SyncVars don't longer exist in MLAPI. Convert your existing SyncVars into [NetworkVariables](../mlapi-basics/networkvariable).

### Troubleshooting

#### The type or namespace name 'MLAPI' could not be found

This error will pop up if your project uses Assembly definition (.asmdef) files. After switching to the package version your assembly definition files will need to reference `com.unity.multiplayer.mlapi.runtime`.

#### The type or namespace name 'NetworkedBehaviour' could not be found

If you get an error message like this (or for another MLAPI type than `NetworkedBehaviour`) in the console it is most likely because your code contains outdated APIs. Open the script indicated in the error messagea and update all APIs to the new names. You can find a table of what we renamed in the [changelog](TODO link doesn't exist yet?).


## 5.**Removing the Patcher Package**
After you are done upgrading your project, you can remove the MLAPI Patcher package from your project in the Unity Package Manager as it is no longer needed.