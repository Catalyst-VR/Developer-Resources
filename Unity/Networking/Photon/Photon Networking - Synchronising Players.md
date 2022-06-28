
# Photon Networking: Synchronising Players



## Syncing Object Transformations Between Clients:



-Add a 'Photon View' and 'Photon Transform View' component to the gameobject you want to sync

-Tick the variables you want to sync on the Photon Transform View (Postion, Rotation, Scale)

[There are also Photon Animator View and Photon Rigidbody View components which synchronise an objects animation and physics respectively] 

Keep in mind this synchronisation is only one way, the client with ownership over the Photon View component sends the transform information to all other clients.

The client with ownership will usually be the host that made the lobby; unless the object was instantiated by a different client.

The only way to allow all clients to send tranformation information is by transferring ownership of the object to whichever client last manipulated it.

This can be done through scripting only if the 'Ownership Transfer' variable is set to Takeover or Request. Although it is typically easier to have the host handle transform logic in the first place.



## Spawning Players:

-Create a folder in your assets folder called 'Resources'

-Give an object a Photon View and save it as a prefab in the Resources folder, give it the name 'Player'

-Create a new C# script, name it something like 'PlayerInstantiation' and copy this into it

```csharp
using Photon.Pun;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerInstantiation : MonoBehaviour
{

    public static void SpawnPlayer()
    {
        PhotonNetwork.Instantiate("Player", GenerateStartingPosition(), new Quaternion(0,0,0,0));
    }

    public static Vector3 GenerateStartingPosition()
    {

        Vector3 playerPos = new Vector3(Random.Range(-4f, 4f), 0, Random.Range(-4f, 4f));
         
        return playerPos;
    }

}
```
-Here a new Player object is being spawned at a random location each time a new player joins the game



## Allowing For Different Player Input:


-Complete 'Spawning Players' before starting

-Add a Photon Transform View to your Player prefab

-Create a basic movement script, name it 'Movement' and attatch it to your Player prefab

-Make a new C# script called 'PlayerSetup', attatch it to your Player prefab and copy this into it


```csharp
using Photon.Pun;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerSetup : MonoBehaviour
{
    [SerializeField]
    Movement moveScript;

    [SerializeField]
    PhotonView playerView;

    void Awake()
    {
        if(!playerView.IsMine)
        {
            moveScript.enabled = false;
        }
    }
}
```

-This script disables the Movement script if the client isn't the creator of this Player object, allowing them only to control themselves


