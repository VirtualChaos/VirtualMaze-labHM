# Ray Casting guide
This utility takes eye-tracking data and generates information about the fixated surfaces or objects.
![data-generationScreenshot](/docs/images/data-generation.PNG)

## Table of Contents
- [Prerequisites](#prerequisites)
- [Steps to Use](#Steps-to-Use)
- [Output](#output)
- [Description of Columns](#Description-of-Columns)
- [Unity Units](#Unity-Units)
- [Object Size Reference](#Object-Size-Reference)
- [Object Name References](#Object-Name-References)
- [Gaze Position in Object](#Gaze-Position-in-Object)
- [Environment Reference](#Environment-Reference)
- [Synchronization](#Synchronization)

## Prerequisites
- Complete [Setting Up](/docs/DeveloperGuide.md#setting-up) in the Developer's Guide.

- **Session data file:** Session file generated by VirtualMaze (Unity) during the experiment.

- **.edf file:** .edf file from the EyeLink eye-tracker.

#### Important
The screen size of the computer running the data generation must be the same as the screen size of the one used in the experiment.

## Steps to Use
1. Open the VirtualMaze project in Unity.
2. Select the File->Open Scene menu command.
3. Navigate to VirtualMaze->Assets->Scenes and select Start.unity.
4. Select the files in the respective text boxes in the GUI.
5. Select the destination folder to save the output file.
6. Click ***Generate*** to start processing the files.

###### Note:
Compiling the game will speed up the generation of ray cast data because the computer does not have to deal with the overhead of the Unity Editor.

To compile the game, Click on *File > Build and Run*.

## Using your own scenes
To run this utility on your own scenes:

1. Open your project in Unity.
2. Open up your project view, and *Right-click > Select Dependencies*.
3. *Right-click > Export Package*. In the Export Package Window, unselect all scripts other than the ones that are required for the scene to function.
4. Open VirtualMaze in Unity.
5. Import the package you just exported.
6. Make any necessary changes to ScreenSaver.cs, and add the new scene to the build settings (*File > Build Settings*).

VirtualMaze finds the name of the scene to load from the session file. Make sure that the scene is included in the build settings.

If you would like to modify this utility, the source code can be found in *ScreenSaver.cs*.

## Eyelink Data Input Types
VirtualMaze accepts 2 formats for reading Eyelink Data Files (.edf) and Comma Separated Value (.csv) files.

To use a .csv file, the file must **NOT** have any headers and the columns must be defined as follows:

1. Timestamp (uint).
2. FEVENT.type (int, 200 if it is a FSAMPLE type as defined in the EDF_ACCESS_API documentation from SR Research).
3. FSAMPLE.gx (float, 0 or empty if the current row is a FEVENT type).
4. FSAMPLE.gy (float, 0 or empty if the current row is a FEVENT type).
5. message (string, empty if the current row is a FSAMPLE type).

##### Note
If a row is detected to be a FEVENT Type, the gx and gy column will be ignored and as for FSAMPLE, the message column will be ignored.

#### Example
```
#FEVENT
2204779,24,0,0,Trigger Version 84
```

```
#FSample
2204775,200,1241.2,-48.2,
```

## Output
A CSV (Comma Separated Value) file will be generated in the destination folder.

![data-generation-output Screenshot](/docs/images/data-generation-output.PNG)


#### Description of Columns
1. Type of data in the row (string/text).
2. Timestamp of the gaze data used to identify the fixated object (unsigned int)
3. Name of the object fixated by the gaze, or message received by EyeLink (string/text).

*Raw Gaze Data (Pixels)*

4. gx data from the .edf file used to raycast.
5. gy data from the .edf file used to raycast.

*Subject Location in Worldspace ([Unity Units](#unity-units))*

6. X coordinate of the subject's location in Worldspace.
7. Y coordinate of the subject's location in Worldspace.
8. Z coordinate of the subject's location in Worldspace.
9. Orientation of the subject about Unity's Y axis (degrees).

*Gaze Location ([Unity Units](#unity-units))*

10. X Worldspace coordinate where the gaze lands.
11. Y Worldspace coordinate where the gaze lands.
12. Z Worldspace coordinate where the gaze lands.

*Gazed Object Location ([Unity Units](#unity-units))*

13. X Worldspace coordinate of the center of the fixated object.
14. Y Worldspace coordinate of the center of the fixated object.
15. Z Worldspace coordinate of the center of the fixated object.

*Gaze Position in Object ([Unity Units](#unity-units))*

16. 2D X location of gaze with reference to the center of the fixated object.
17. 2D Y location of gaze with reference to the center of the fixated object.

See [Gaze Position in Object](#gaze-position-in-object) for more details.

#### Unity Units
Unity Units are values Unity uses to position the various game objects in Worldspace, where the center of the floor of the maze is located at (x: 0, y: 0, z: 0). For reference and scaling, the "rooms" used in VirtualMaze are 25 by 25 Unity Units and the ceiling is 4.93 Unity Units high.

The default settings for units in Unity is 1 Unity Unit = 1 meter. However, because the scaling is mutable, feel free to scale these values as required.

#### Object Size Reference

- **Posters:** width: 2.24, height: 1.4, thickness: 0 Unity Units
- **Outer Walls:** width: 5, height: 5, thickness: 0.1 Unity Units
- **Inner Walls/Barriers:** width: 5, height: 3.11, thickness: 0.1 Unity Units
- **Ground and Ceiling:** width: 25, length: 25, thickness: 0 Unity Units
- Ceiling Height: 4.93 Unity Units
- **Cue Image:** width: 0.4, length: 0.2, thickness: 0 Unity Units
- **Hint Image** width: 0.2, length: 0.1, thickness: 0 Unity Units
- **Height of Viewport:** 1.85 Unity Units from the floor

See [Cue Image and Hint Image ](#cueImage-and-hintImage) for definition of Cue Image and Hint Image.

#### Object Name References

##### Cue Image and Hint Image
![cue-hint-image](/docs/images/cue-hint-image.png)

There are 2 kinds of cues presented to the subject, and for convenience, the larger cue is referred to as the Cue Image, while the smaller image at the top is referred to as the Hint Image.

## Gaze Position in Object
Gaze Position in Object represents the coordinates of the gaze position from the center of the object.

###### Image Cue in 3D Space
![relative position explanation](/docs/images/relativePos-explaination.png)

The center of the object (represented by the circle) is the 3D coordinates of its position in virtual space.

The point where the gaze hits the object (represented by the triangle) is also represented by its position in virtual space.

For the example shown in the image, the resultant Gaze Position in Object is **(x: 1, y: 1)** because the center of the 2D image is taken to be the origin of the 2D coordinate system.

###### Gaze point with reference to the center of the Cue Image.
![reading-relative-position](/docs/images/reading-relative-position.png)

### Environment Reference

#### Axes

![Unity Axes](/docs/images/axes.PNG)

This is the axes defined by Unity. The colored sides represents the positive direction of the axes and the Y axis represents the height of the axis.

#### Rotation Around Y Axis

![rotation-ref](/docs/images/rotation-ref.png)

The direction of the black arrow represents the positive increase in rotational angle around Y axis starting from 0.


#### Room
The surrounding walls of the room are labeled as follows.

![wall-label](/docs/images/wall-positions.png)

### Synchronization
The time between messages may not be the same in the session log and .edf file because the sessions files are generated from Unity, which runs on a non-real-time operating system, while the EyeLink uses something more like a real-time operating system.

In order to synchronize the timelines in the 2 files, we correct the event times in Unity with those we find in EyeLink, and adjust all inter-event times in Unity by dividing the inter-event interval differences by the number of frames between the 2 triggers.

#### Missing Triggers
In the event of missing triggers either in the session files or the .edf file, the difference in the interval between the previous and the next trigger (w.r.t. the missing trigger) is first computed. The missing trigger is approximated if the difference in interval is within **20ms**. Otherwise, the data between the previous and the next trigger is ignored.

If the data is ignored, the gaze data for that point is ignored, and the following line will be recorded in the output CSV:

```CSV
SAMPLE_TYPE,4558794,Data ignored (x:1267.1 y:219.4),,,,,,,,,,,,,,
```

The message will also include the gaze x and y position from the edf file.

If the trigger is approximated, the following line will be added with the approximated time:

```CSV
EVENTMESSAGE,4558794,Approximated Trigger 13,,,,,,,,,,,,,,
```
The approximated trigger message will also include the trigger number it approximated.