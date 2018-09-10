---
id: 87
title: Remove the used Components manually from Visual Studio for Mac
date: 2018-03-07T20:40:37+00:00
author: trinnguyen
layout: post
permalink: /remove-the-used-components-manually-from-visual-studio-for-mac/
categories:
  - Xamarin
tags:
  - nuget
  - remove component
  - xamarin
---
New release of Visual Studio 4 Mac 7.4 on March 2018 has introduced new changes on C# editing which bring the significant improvement on typing responsiveness but forces old Xamarin projects to remove the Xamarin Components.<figure id="attachment_90" style="width: 600px" class="wp-caption aligncenter">

<img class="wp-image-90" src="/wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-upgrade.png" alt="" width="600" height="183" srcset="/wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-upgrade.png 719w, /wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-upgrade-300x91.png 300w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /><figcaption class="wp-caption-text">Popup by Visual Studio 4 Mac 7.4</figcaption></figure> 

The message:

_&#8220;We&#8217;ve detected that your solution is currently using Xamarin Components, which is no longer supported. Please remove the used Components manually and reopen the solution.&#8221;_

We can Fix it by removing the Xamarin Component and replaced by the Nuget package or another library/DLL

# Remove the Component

VS4Mac 7.4 removes the Components section of the Solution Pad so we have to work manually with the .csproj file to remove the Component, for example, <a href="https://components.xamarin.com/view/coreplot" target="_blank" rel="noopener">CorePlot Component</a>

  * Open your .csproj file with an XML editor (Visual Studio Code/ Atom,…), find the section \`**XamarinComponentReference\`**, under a parent XML node called \`**ItemGroup\`**, remove this entire node from the file<figure id="attachment_88" style="width: 498px" class="wp-caption aligncenter">

<img class="wp-image-88 size-full" src="/wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-remove-csproj-1.png" alt="" width="498" height="108" srcset="/wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-remove-csproj-1.png 498w, /wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-remove-csproj-1-300x65.png 300w" sizes="(max-width: 498px) 85vw, 498px" /><figcaption class="wp-caption-text">Remove XamarinComoponetReference</figcaption></figure> 

  * Still, inside .csproj file, find the reference to the DLL and delete it by searching for the component name, for example: “CorePlot”, should found a node \`**<Reference Include=&#8221;CorePlotiOS&#8221;>**\`, simple delete entire node<figure id="attachment_89" style="width: 600px" class="wp-caption aligncenter">

<img class="wp-image-89" src="/wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-remove-csproj-2.png" alt="" width="600" height="127" srcset="/wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-remove-csproj-2.png 851w, /wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-remove-csproj-2-300x63.png 300w, /wp-content/uploads/2018/03/trinnguyen.com-xamarin-components-remove-csproj-2-768x162.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /><figcaption class="wp-caption-text">Remove Xamarin Component DLL reference</figcaption></figure> 

  * Now back to VS4Mac and reopen your solution

## Adding the library back:

&#8211; If you could find the appropriate NuGet package for that, would be great!
  
&#8211; Or look for another alternate library, in my case, considering OxyPlot
  
&#8211; Create your owned package if the library is a binding to a native (ObjC/Java) library
  
&#8211; Or keeping the DLL from Components folder and add the reference to it (not recommended)