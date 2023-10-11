# Unity-ML-LLAPI
Instructions to interact with a Unity environment with ML agents using Python Low-level API

  1. Go to Unity Hub and create a new 3D project
  2. In Unity, go to `Window`, then choose `Package Manager`. At the top you can see `Packages: In Project`, click on it and choose `Packages: Unity Registry`. Now look for `ML Agents`. Install the package.
     
   <img width="801" alt="Screenshot 2023-10-11 at 14 56 59" src="https://github.com/AmineAndam04/Unity-ML-LLAPI/assets/49843367/de4bb522-4ee7-4fa1-93a4-ba0c54fdbc9d">
   
  3. close Unity and re-open it again.
  4. Create a suitable environment. (We use the environment below) :

<img width="1165" alt="Screenshot 2023-10-11 at 15 15 20" src="https://github.com/AmineAndam04/Unity-ML-LLAPI/assets/49843367/2c2b1433-53de-4716-a579-eb21ecd65b59">

     
  5. Create a new c# script, at its header add `using Unity.MLAgents ;`, and change the `MonoBehaviour` to `Agent`. 

<img width="520" alt="Screenshot 2023-10-11 at 15 24 53" src="https://github.com/AmineAndam04/Unity-ML-LLAPI/assets/49843367/d194f3b1-e7fc-4bfb-ae07-da465369c21f">

 6. Add the appropriate logic you want. In our case, we aim to train our agent (the cube) to reach a goal (the sphere). Our code is as follows:

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents ;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;



public class AgentScript : Agent
    
{
    
    [SerializeField] private Transform  targetTransform ;

    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(transform.localPosition);
        sensor.AddObservation(targetTransform.localPosition);
    }

    public override void OnActionReceived(ActionBuffers actions)
    {
        float moveX = actions.ContinuousActions[0];
        float moveZ = actions.ContinuousActions[1];
        float moveSpeed = 7f; 
        transform.localPosition += new Vector3(moveX,0,moveZ)*Time.deltaTime*moveSpeed ;
    }
    public override void OnEpisodeBegin()
    {
        transform.position = new Vector3 (0f,1f,0f);
    }

    private void OnTriggerEnter(Collider other) {
        if (other.tag == "Wall")
        {
            Debug.Log("Collision with the Wall");
            SetReward(-1f);
            EndEpisode();
        }

        if (other.tag == "Goal")
        {
            Debug.Log("Collision with the goal");
            SetReward(1f);
            EndEpisode();
        }
    }
}
```
 7. Select the object to which you assigned the agent script. In the inspector window set the parameters for the Behavior Parameters :
 8. 
    <img width="551" alt="Screenshot 2023-10-11 at 15 31 02" src="https://github.com/AmineAndam04/Unity-ML-LLAPI/assets/49843367/d8d9f353-fb21-4dd1-bb29-29a56bb651e5">

 9. For now, we only have numerical observations. In some scenarios, we may be interested in having visual observations, i.e. to use an architecture with CNNs. In the inspector of your agent, go to Add Component, and look for Camera Sensor.

<img width="330" alt="Screenshot 2023-10-11 at 15 38 54" src="https://github.com/AmineAndam04/Unity-ML-LLAPI/assets/49843367/1a4b6f7b-a8a2-4610-91f7-e159d2171424">

 10. Set your camera in a suitable location to capture relevant observations that may help your model:

<img width="867" alt="Screenshot 2023-10-11 at 15 39 50" src="https://github.com/AmineAndam04/Unity-ML-LLAPI/assets/49843367/19df5eb1-d65c-4bce-a098-05ea49d96e5b">

11. In the Hierarchy panel, duplicate the camera, and change its name (we changed it to SensorCamera.It's advisable to disable the Audio Listener in the inspector of SensorCamera. Then add the duplicated camera to the Camera field in the Camera Sensor field in the inspector of the agent:

<img width="338" alt="Screenshot 2023-10-11 at 15 44 14" src="https://github.com/AmineAndam04/Unity-ML-LLAPI/assets/49843367/e2822f96-149a-45b1-9994-970980b7592e">

    
13. 

