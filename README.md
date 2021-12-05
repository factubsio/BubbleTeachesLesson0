# Download the WrathModificationTemplate.zip and unzip it to a nicely named folder:

<img width="618" alt="folder_setup" src="https://user-images.githubusercontent.com/65080026/144756421-3c10d642-cd2c-4071-85b0-7a9e351e914f.PNG">

# Open Unity Hub and add a new project:

<img width="720" alt="add_unity_project" src="https://user-images.githubusercontent.com/65080026/144756432-4038adc8-f837-4041-a462-58f74837aa9d.PNG">

then

<img width="583" alt="select_fodler" src="https://user-images.githubusercontent.com/65080026/144756445-f8925c94-d033-4b05-a07d-b8cdd8d4f483.png">

# Update the project to the correct Unity version:

<img width="552" alt="update0" src="https://user-images.githubusercontent.com/65080026/144756460-1387698e-7ae9-4268-b981-e6f2bf556344.PNG">

# Open the project (click on it) and confirm the update:

<img width="583" alt="update2" src="https://user-images.githubusercontent.com/65080026/144756461-d631514e-0b9a-4494-8aec-394d7867321e.PNG">

# Do one-time Unity setup, give it your Wrath installation folder when prompted:

<img width="630" alt="setup0" src="https://user-images.githubusercontent.com/65080026/144756514-d3e914e2-482c-4b59-abc0-fcc39f66da74.PNG">

# Close Unity, then re-open the project from the hub

# Do second part of one-time Unity setup

<img width="401" alt="setup2" src="https://user-images.githubusercontent.com/65080026/144756564-6dce9602-bdd9-4c7e-971e-a64c37b2db7c.PNG">

# Delete the Example folder
<img width="514" alt="delete_example" src="https://user-images.githubusercontent.com/65080026/144757747-348ee8c9-45b5-4b23-b6f0-f74ad50f9d5a.PNG">

# Create a new folder, call it something useful:

<img width="698" alt="createexamplefolder" src="https://user-images.githubusercontent.com/65080026/144757754-82d3f40f-0a22-4ab5-8d37-5e495db544e5.PNG">

# Open the folder (single click in the tree-view on left, or double click on the folder in asset view in middle):

# Create the folders `Content`, `Blueprints`, `Localization`, and `Scripts`

<img width="615" alt="create_folders" src="https://user-images.githubusercontent.com/65080026/144757766-359ce400-bd38-4cdd-8005-ac8f3f99a08d.PNG">

# Add a modification object:

<img width="605" alt="add_modification" src="https://user-images.githubusercontent.com/65080026/144757771-ee9c2ab6-e538-4625-aa5d-7cbf9fd27191.PNG">

# Select the modification object:

<img width="1522" alt="edit_modification" src="https://user-images.githubusercontent.com/65080026/144757780-c699124e-89ac-4b68-80d0-1ace33154bc2.PNG">

# Put in some stuff:

<img width="577" alt="edit_modification_data" src="https://user-images.githubusercontent.com/65080026/144757788-4fb80a5f-2d34-4a22-a29c-cd666c3be4a4.PNG">

# NOTE - EVERYTHING IS THE SAME, add it, select it, edit it

# Add an asmdef

<img width="467" alt="create_asmdef" src="https://user-images.githubusercontent.com/65080026/144757799-7c4e4be2-f8df-48c5-84c0-c2afaa80453d.PNG">

# Add a script:

<img width="412" alt="create_script" src="https://user-images.githubusercontent.com/65080026/144757805-322bf25f-0da5-4d1b-81e3-e1c6582e28fb.PNG">

# Open it, delete current contents, enter:
```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PortraitInjector
{
    [OwlcatModificationEnterPoint]
    public static void ModEntryPoint() {

    }
}
```

# Fix the error

<img width="595" alt="fix_problems" src="https://user-images.githubusercontent.com/65080026/144757838-f8ac2e1b-cb75-4e66-9591-80910084379c.PNG">


# Add rest of the code:
```cs
using System.Collections;
using System.Collections.Generic;
using System.Reflection;
using HarmonyLib;
using Kingmaker.Blueprints.JsonSystem;
using Kingmaker.Modding;
using UnityEngine;

public class Main
{
    public static OwlcatModification ModDetails;
    public static Harmony HarmonyHandle;

    public static void Log(string message) => ModDetails.Logger.Log(message);
    public static void Log(string fmt, params object[] args) => ModDetails.Logger.Log(fmt, args);

    [OwlcatModificationEnterPoint]
    public static void ModEntryPoint(OwlcatModification modDetails) {
        // Save the details so we can access them later
        ModDetails = modDetails;

        // Create a new harmony instance with our unique name
        HarmonyHandle = new(modDetails.Manifest.UniqueName);

        // Ask harmony to find all [HarmonyPatch] methods in this assembly and apply them
        HarmonyHandle.PatchAll(Assembly.GetExecutingAssembly());
    }
}

[HarmonyPatch(typeof(BlueprintsCache), nameof(BlueprintsCache.Init))]
public static class AddBlueprintsInCode {

    [HarmonyPostfix]
    public static void Init() {
        Main.Log("Adding portraits");
    }
}
```

# Build the mod

<img width="470" alt="build" src="https://user-images.githubusercontent.com/65080026/144757867-251a507e-9854-457a-a2bc-aebd2b969dc8.PNG">


# That took too long, let's fix it. MAKE SURE YOU HAVE VISUAL STUDIO COMMUNITY C# INSTALLED
## AGAIN: MAKE SURE YOU HAVE VISUAL STUDIO COMMUNITY 2019 C# INSTALLED

# Edit `Assets/Editor/Build/Tasks/BuildAssemblies.cs`, replace the `Run` method with:

```cs
	public ReturnCode Run()
		{
			var msBuildPath = @"C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe";
			string projectPath = Path.Combine(
				Directory.GetCurrentDirectory(),
				"BubbleLesson0_Portraits.csproj"
			);

			var settings = m_BuildParameters.GetScriptCompilationSettings(); 
			string outputFolder = m_BuildParameters.GetOutputFilePathForIdentifier(BuilderConsts.OutputAssemblies);
      
			if (File.Exists(msBuildPath) && File.Exists(projectPath))
			{
				ProcessStartInfo msBuildInfo =
					new ProcessStartInfo(msBuildPath, $"{projectPath} /p:OutputPath={outputFolder}");
				var msBuild = Process.Start(msBuildInfo);
				msBuild.WaitForExit();
			}
			else {
				var results = PlayerBuildInterface.CompilePlayerScripts(settings, outputFolder);
				if (results.assemblies == null || !results.assemblies.Any())
				{
					return ReturnCode.Error;
				}
			}

			string[] asmdefGuids = AssetDatabase.FindAssets("t:Asmdef", new[] {m_ModificationParameters.ScriptsPath});
			string[] asmdefNames = asmdefGuids
				.Select(AssetDatabase.GUIDToAssetPath)
				.Select(AssetDatabase.LoadAssetAtPath<AssemblyDefinitionAsset>)
				.Select(GetAssemblyName)
				.ToArray();
			foreach (string filePath in Directory.GetFiles(outputFolder))
			{
				if (asmdefNames.All(i => !filePath.Contains(i)))
				{
					File.Delete(filePath);
				}
			}

			return ReturnCode.Success;
		}
```

# We also need to make sure any stuff we add will be bundled even without a reference, edit `Assets/Editor/Build/Tasks/PrepareBundles.cs`:

# Replace this:

```cs
				string assetPath = AssetDatabase.GUIDToAssetPath(assetGuid);
				string bundleName = m_LayoutManager.GetBundleForAssetPath(assetPath, m_ModificationParameters.TargetFolderName);
				if (bundleName == null)
				{
					continue;
				}

				if (!m_Tracker.UpdateInfo(assetPath))
				{
					return ReturnCode.Canceled;
				}
```

# with this:
```cs
				string assetPath = AssetDatabase.GUIDToAssetPath(assetGuid);
				string bundleName = m_LayoutManager.GetBundleForAssetPath(assetPath, m_ModificationParameters.TargetFolderName);
				if (bundleName == null || bundleName.EndsWith("_content"))
				{
					bundleName = "bubblelessons_all";
				}

				if (!m_Tracker.UpdateInfo(assetPath))
				{
					return ReturnCode.Canceled;
				}
```

# Now build the mod again, and be amazed that it only takes like one second instead of 30.

 * Copy the `Build/BubbleTeaches_Lesson0Portraits` folder from thto `user-folder/AppData/LocalLow/Owlcat Games/Pathfinder Wrath Of The Righteous/Modifications`
 * Add your modification to user-folder/AppData/LocalLow/Owlcat Games/Pathfinder Wrath Of The Righteous/OwlcatModificationManagerSettings.json

```json
{
    "EnabledModifications": ["BubbleTeaches_Lesson0Portraits"]
}
```

 * Run Pathfinder: Wrath of the Righteous

# Open the log file: `user-folder/AppData/LocalLow/Owlcat Games/Pathfinder Wrath Of The Righteous/GameLogFull.txt`, search for `Bubble`, you should see:

```
[55.3343 - Mods]: Load modifications' descriptors: C:/Users/worce/AppData/LocalLow/Owlcat Games/Pathfinder Wrath Of The Righteous\Modifications
[55.3413 - Mods]: Apply modification: BubbleTeaches_Lesson0Portraits (C:/Users/worce/AppData/LocalLow/Owlcat Games/Pathfinder Wrath Of The Righteous\Modifications\BubbleTeaches_Lesson0Portraits)
[55.3443 - Mods]: Load assembly: C:/Users/worce/AppData/LocalLow/Owlcat Games/Pathfinder Wrath Of The Righteous\Modifications\BubbleTeaches_Lesson0Portraits\Assemblies\BubbleLesson0_Portraits.dll
[55.3463 - Mods]: Bundle found: C:/Users/worce/AppData/LocalLow/Owlcat Games/Pathfinder Wrath Of The Righteous\Modifications\BubbleTeaches_Lesson0Portraits\Bundles\BubbleTeaches_Lesson0Portraits_BlueprintDirectReferences
[55.3483 - Mods]: Initialize assembly: BubbleLesson0_Portraits, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null
[55.4953 - BubbleTeaches_Lesson0Portraits]: Adding portraits
```

*hashtag-success*

# Add a portrait, the blueprint:

We need a "GUID" for the blueprint, this is just a (very) unique name to make sure if someone else adds a blueprint we don't clash.

There are a bunch of ways to generate GUIDs, for the moment just go to https://guidgenerator.com/online-guid-generator.aspx and click generate, you should get something like:

`024261bd-c6e9-4e55-a521-0fe4eb4f8xyz` - NOTE: This is intentionally invalid so you don't copy it.

# Edit our code in `PortraitInjector.cs`:

```cs

[HarmonyPatch(typeof(BlueprintsCache), nameof(BlueprintsCache.Init))]
public static class AddBlueprintsInCode {

    [HarmonyPostfix]
    public static void Init() {
        Main.Log("Adding portraits");

        var myPortrait = new BlueprintPortrait {
            name = "mewsifer-portrait",
            AssetGuid = BlueprintGuid.Parse("c1874b9f-f84b-40b4-a9a1-1eed9eb16322"),
            Data = new PortraitData {
                PortraitCategory =  Kingmaker.Enums.PortraitCategory.Wrath,
                m_FullLengthImage = new SpriteLink { AssetId = "" },
                m_HalfLengthImage = new SpriteLink { AssetId = "" },
                m_PortraitImage = new SpriteLink { AssetId = "" },
            }
        };
    }
}
```




