# Asset Bundles: Getting Started



## How To Create An Asset Bundle:

-Install the 'Addressables' package from the package manager 

-Create an 'Addressable Settings' script object (Window -> Asset Management -> Addressables -> Groups)

-Create a folder called 'Editor' and make a script within that folder (called something like BundleBuilder)

-Include this code in that script **make sure to replace the examplepath and build target params with appropriate ones**

    using UnityEditor;
    public class BundleBuilder : Editor
    {
        [MenuItem("Assets/ Build AssetBundles")]

        static void BuildAllAssetBundles()
        {
            BuildPipeline.BuildAssetBundles("C:/ExamplePath", BuildAssetBundleOptions.ChunkBasedCompression, BuildTarget.StandaloneWindows64);
        }
    }

-Assign all of the assets you want to be in a bundle to a bundle. Do this by selecting the assets then choosing a bundle with the dropdown menu at the bottom of the inspector

-Now click on 'Build AssetBundles'(Assets -> Build AssetBundles) and your bundle will be in the path you specified earlier in the 'BundleBuilder' script



## How To Load An Asset Bundle (Web Remote or Locally):

-Create a new script and include this code

    public string bundleURL;
    IEnumerator Start()
    {
        using (WWW web = new WWW(bundleURL))
        {
            //Retrieve bundle
            yield return web;
            AssetBundle remoteAssetBundle = web.assetBundle;
            if (remoteAssetBundle == null)
            {
                Debug.LogError("Failed To Download");
                yield break;
            }
            
            //Place object in the scene
            Instantiate(remoteAssetBundle.LoadAsset("ExampleAsset"));

            //Finish bundle retrieval
            remoteAssetBundle.Unload(false);
        }
    }

-This code will download the bundle based on url set in 'bundleURL' and place a chosen object from the bundle ("ExampleAsset") in the scene



## Showing Download Progress:
        
-Replace the loading asset bundle script with this one (make sure to include 'using UnityEngine.Networking;' at the top)

    IEnumerator Start()
    {
        string url = bundleURL;
        using (var uwr = new UnityWebRequest(url, UnityWebRequest.kHttpVerbGET))
            {
                uwr.downloadHandler = new DownloadHandlerAssetBundle(url, 36, 0);
                
                UnityWebRequestAsyncOperation operation = uwr.SendWebRequest();
                while (!operation.isDone)
            	{
                	//Use operation.progress in here to get the current progress of the download
                	yield return null;
            	}

            	AssetBundle bundle = DownloadHandlerAssetBundle.GetContent(uwr);

            	Instantiate(bundle.LoadAsset("ExampleAsset"));
            	bundle.Unload(false);
        }
    }

-It is mostly the same script except it uses UnityWebRequest instead of WWW as it allows you to obtain the progress of the download operation

-In the while loop, using 'operation.progress' will return a float denoting how complete the download is - Use this to update sliders or text objects with the current progress

