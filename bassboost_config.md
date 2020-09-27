# JSON schema

## Controller Object

| Key                  | Value type | Value description                                       |
| -------------------- |:----------:| :-------------------------------------------------------|
| controllerType       | String     | Type of the controller. Possible values: Spotify, Timer |
| controllerDescriptor | [Spotify Controller Object](#Spotify-Controller-Object) or [Timer Controller Object](#Timer-Controller-Object)          |  Describes the controller based on the controllerType |

*If the value of the `controllerType` is `Timer`, the `controllerDescriptor` value is a [Timer Controller Object](#Timer-Controller-Object), otherwise it's a [Spotify Controller Object](#Spotify-Controller-Object)*

```json
{
    "controllerType": "Timer",
    "controllerDescriptor": TimerControllerObject
}
```

### Spotify Controller Object

| Key                   | Value type                                              | Value description                   |
| -------------------   |:-------------------------------------------------------:| :-----------------------------------|
| initialActions        | [Action Object](#Action-Object)[]                       | Describes those actions that are triggered before any event occurs |
| trackSegmentActions   | [Action Object](#Action-Object)[]                       | The actions that are triggered if a track segment occurs |
| beatActions           | [Action Object](#Action-Object)[]                       | The actions that are triggered if a beat occurs |
| sectionActions        | [Action Object](#Action-Object)[]                       | The actions that are triggered if a section occurs |
| timerActions          | [Timer Action Object](#Timer-Action-Object)[]           | The actions that are triggered based on timer events |

```json
{
    "initialActions": Action[],
    "trackSegmentActions": Action[],
    "beatActions": Action[],
    "sectionActions": Action[],
    "timerActions": TimerAction[]
}
```

### Timer Controller Object

| Key                   | Value type                                              | Value description                   |
| -------------------   |:-------------------------------------------------------:| :-----------------------------------|
| initialActions        | [Action Object](#Action-Object)[]                       | Describes those actions that are triggered before any event occurs |
| timerActions          | [Timer Action Object](#Timer-Action-Object)[]           | The actions that are triggered based on timer events |

```json
{
    "initialActions": Action[],
    "timerActions": TimerAction[]
}
```

### Timer Action Object

| Key             | Value type                        | Value description                                       |
| --------------- |:---------------------------------:| :-------------------------------------------------------|
| initialDelay    | Integer                           | The delay before the very first action execution in milliseconds |
| repetitionDelay | Integer                           | The delay between the action executions in milliseconds |
| actions         | [Action Object](#Action-Object)[] | The actions that should be triggered after the delay time is elapsed |

```json
{
    "initialDelay": 500,
    "repetitionDelay": 1000,
    "actions": Action[]
}
```

### Action Object

| Key              | Value type | Value description                            |
| ---------------- |:----------:| :--------------------------------------------|
| actionType       | String     | Type of the action. Possible values: CreateSegment, TransformSegment |
| actionDescriptor | [Create Segment Action Object](#Create-Segment-Action-Object) or [Transform Segment Action Object](#Transform-Segment-Action-Object) | Describes the action based on actionType   |

*If the value of the `actionType` is `CreateSegment`, the `actionDescriptor` value is a [Create Segment Action Object](#Create-Segment-Action-Object), otherwise it's a [Transform Segment Action Object](#Transform-Segment-Action-Object)*

```json
{
    "actionType": "CreateSegment",
    "actionDescriptor": CreateSegmentObject
}
```

### Create Segment Action Object

| Key           | Value type                              | Value description                               |
| ------------- |:---------------------------------------:| :-----------------------------------------------|
| headPosition  | Integer                                 | The position of the first LED of the segment. It might be off the strip in both directions. The strip is indexed from 1 to the length of the strip. If the position of the head is not within these bounds, the rest of the segment could still be visible. If nothing is visible from the segment it's automatically deleted |
| length        | Integer                                 | Optional. The number of LEDs the build up the segment. If not specified, the length is calculated by the duration of the rhythm that triggered the action and the segment's current move properties. If the segment doesn't have move properties or the segment creation is not triggered by a rhythm, it defaults to 1 |
| headColor     | String                                  | The color of the first LED of the segment       |
| headIntensity | Float                                   | The light intensity of the first LED of the segment. It should be in the range [0.0, 1.0]. If not, the value will be the closest number that is in the range     |
| priority      | Integer                                 | The priority of the segment. Reserved for futures use                     |
| transforms    | [Transform Wrapper Object](#Transform-Wrapper-Object)[] | Describes the initial transformations of the segment applied right after the creation |

```json
{
    "headPosition": 8,
    "length": 16,
    "headColor": "orange",
    "headIntensity": 0.8,
    "priority": 0,
    "transforms": TransformWrapper[]
}
```

### Transform Wrapper Object

| Key       | Value type   | Value description                                |
| --------- |:------------:| :------------------------------------------------|
| transform | Transform    | Contains a [Transform Object](#Transform-Object). Its sole purpose is to make the parsing easier. |


```json
{
    "transform": Transform
}
```

### Transform Segment Action Object

| Key             | Value type                            | Value description                                  |
| --------------- |:-------------------------------------:| :--------------------------------------------------|
| segmentSelector | String                                | Determines which segments are transformed on the LED strip. Possible values: All, EverySecond, Random.|
| transform       | [Transform Object](#Transform-Object) | Describes the transformation applied to the selected segments |

```json
{
    "segmentSelector": "All",
    "transform": Transform
}
```

### Transform Object

| Key                 | Value type | Value description |
| ------------------- |:----------:| :-----------------|
| transformType       | String     | Describes the type of the transformation. Possible values: Move, Dim, Fade |
| transformDescriptor | [Move Transform Object](#Move-Transform-Object) or [Dim Transform Object](#Dim-Transform-Object) or [Fade Transform Object](#Fade-Transform-Object) | Describes the transformation based on the transformType |

*If the value of the `transformType` is `Move`, the `transformDescriptor` value is a [Move Transform Object](#Move-Transform-Object), if the value is `Dim` the `transformDescriptor` value is a [Dim Transform Object](#Dim-Transform-Object), otherwise it's a [Fade Transform Object](#Fade-Transform-Object)*

```json
{
    "transformType": "Move",
    "transform": MoveTransformObject
}
```

### Move Transform Object

| Key           | Value type | Value description |
| ------------- |:----------:| :-----------------|
| LEDsPerSecond | Integer    | The number of LEDs that the segment travels during a second. Might be negative which means it goes to the opposite of the segment's direction |

```json
{
    "LEDsPerSecond": 300,
}
```

### Dim Transform Object

| Key             | Value type | Value description                                                     |
| --------------- |:----------:| :---------------------------------------------------------------------|
| dimMilliseconds | Integer | Optional. The number of milliseconds that it takes to reach endIntensity. If not specified, the default value is the duration of the rhythm that triggered the transformation or 0 if the transformation is triggered during a segment initialization |
| startIntensity  | Float   | Optional. The intensity of the head segment that the transformation starts from. If it's not the same as the segment's current intensity it immediately changes to this value in the next tick. If not specified, the default value is the segment's current intensity. |
| endIntensity    | Float   | The intensity of the head of the segment after the end of transformation |

```json
{
    "dimMilliseconds": 1000,
    "startIntensity": 0.0,
    "endIntensity": 0.8,
}
```

### Fade Transform Object

| Key              | Value type | Value description                                                    |
| ---------------- |:----------:| :--------------------------------------------------------------------|
| fadeMilliseconds | Integer    | Optional. The number of milliseconds that it takes to reach endColor. If not specified, the default value is the duration of the rhythm that triggered the transformation or 0 if the transformation is triggered during a segment initialization |
| startColor       | LEDColor   | Optional. The color of the head segment that the transformation starts from. If it's not the same as the segment's current color it immediately changes to this value in the next tick. If not specified, the default value is the segment's current color. |
| endColor         | LEDColor   | The color of the head of the segment after the end of transformation |

```json
{
    "fadeMilliseconds": 1000,
    "startColor": 0.0,
    "endColor": 0.8,
}
```

## Example configurations

In this section you can see some example configurations

### First

This one has a single timer action which happens in every 100 ms.
It always creates a segment with a beatiful cyan color and it has a dim
transformation which dims the brighntess to 0 in 100 ms. Basically it's a
stroboscope.

```json
{
    "controller": {
        "controllerType": "Timer",
        "controllerDescriptor":{
            "initialActions": [],
            "timerActions":[
                {
                    "initialDelay": 0,
                    "repetitionDelay": 100,
                    "actions": [
                        {
                            "actionType": "CreateSegment",
                            "actionDescriptor":{
                                "headPosition": 120,
                                "length": 120,
                                "headColor": {
                                    "r": 0,
                                    "g": 255,
                                    "b": 255
                                },
                                "headIntensity": 1.0,
                                "priority": 0,
                                "transforms": [
                                    {
                                        "transform": {
                                            "transformType": "Dim",
                                            "transformDescriptor": {
                                                "dimMilliseconds": 100,
                                                "startIntensity": 1.0,
                                                "endIntensity": 0.0
                                            }
                                        }
                                    }
                                ]
                            }
                        }
                    ]
                }
            ]
        }
    }
}
```

### Second

This one first creates a segment that takes the first 120 LEDs and they are all green. It has a Dim Transformation, so it instantly starts to dim to 0 intensity.
It has also 2 timer actions. The first one has an initial delay set to 3000, so it starts once the green segment finished dimming. it spawns at the beginning
of the strip and has a speed of 100 LED/s. The other time action is very similar
but it has a 3500 ms initial delay so it will start 500 ms later than the first one. It only has 50 LED/s speed. They also have a dim transformation so they are slowly losing brightness as they move.

```json
{
    "controller": {
        "controllerType": "Timer",
        "controllerDescriptor":{
            "initialActions": [
                {
                    "actionType": "CreateSegment",
                    "actionDescriptor":{
                        "headPosition": 120,
                        "length": 120,
                        "headColor": {
                            "r": 0,
                            "g": 255,
                            "b": 0
                        },
                        "headIntensity": 1.0,
                        "priority": 0,
                        "transforms": [
                            {
                                "transform": {
                                    "transformType": "Dim",
                                    "transformDescriptor": {
                                        "dimMilliseconds": 3000,
                                        "startIntensity": 1.0,
                                        "endIntensity": 0.0
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "timerActions":[
                {
                    "initialDelay": 3000,
                    "repetitionDelay": 1000,
                    "actions": [
                        {
                            "actionType": "CreateSegment",
                            "actionDescriptor":{
                                "headPosition": 1,
                                "length": 1,
                                "headColor": {
                                    "r": 255,
                                    "g": 255,
                                    "b": 255
                                },
                                "headIntensity": 1.0,
                                "priority": 0,
                                "transforms": [
                                    {
                                        "transform": {
                                            "transformType": "Move",
                                            "transformDescriptor": {
                                                "LEDsPerSecond": 100
                                            }
                                        }
                                    },
                                    {
                                        "transform": {
                                            "transformType": "Dim",
                                            "transformDescriptor": {
                                                "dimMilliseconds": 1000,
                                                "startIntensity": 1.0,
                                                "endIntensity": 0.0
                                            }
                                        }
                                    }
                                ]
                            }
                        }
                    ]
                },
                {
                    "initialDelay": 3500,
                    "repetitionDelay": 1000,
                    "actions": [
                        {
                            "actionType": "CreateSegment",
                            "actionDescriptor":{
                                "headPosition": 1,
                                "length": 1,
                                "headColor": {
                                    "r": 255,
                                    "g": 0,
                                    "b": 0
                                },
                                "headIntensity": 1.0,
                                "priority": 0,
                                "transforms": [
                                    {
                                        "transform": {
                                            "transformType": "Move",
                                            "transformDescriptor": {
                                                "LEDsPerSecond": 50
                                            }
                                        }
                                    },
                                    {
                                        "transform": {
                                            "transformType": "Dim",
                                            "transformDescriptor": {
                                                "dimMilliseconds": 1000,
                                                "startIntensity": 1.0,
                                                "endIntensity": 0.0
                                            }
                                        }
                                    }
                                ]
                            }
                        }
                    ]
                }
            ]
        }
    }
}
```

### Third

This is a Spotify type controller, which means that it can be synchronized with music. It creates an initial segment at the beginning that dims away in 3 seconds.
When it's synchronized, it always applies a dim transformation to this initially
created segment and dims it from 1 to 0. Note that it doesn't have a dimMilliseconds property which means that it depends on the length of the track
segment that triggered this transformation.

```json
{
    "controller": {
        "controllerType": "Spotify",
        "controllerDescriptor":{
            "initialActions":[
                {
                    "actionType": "CreateSegment",
                    "actionDescriptor":{
                    "headPosition": 120,
                    "length": 120,
                    "headColor": {
                          "r": 0,
                          "g": 255,
                          "b": 0
                        },
            "headIntensity": 1.0,
            "priority": 0,
            "transforms": [
              {
                "transform": {
                  "transformType": "Dim",
                  "transformDescriptor": {
                    "dimMilliseconds": 3000,
                    "startIntensity": 1.0,
                    "endIntensity": 0.0
                  }
                }
              }
            ]
                    }
                }
            ],
            "trackSegmentActions":[
                {
                    "actionType": "TransformSegment",
                    "actionDescriptor": {
                        "segmentSelector": "All",
                        "transform": {
                            "transformType": "Dim",
                            "transformDescriptor": {
                                "startIntensity": 1.0,
                                "endIntensity": 0.0
                            }
                        }
                    }
                }
            ],
            "beatActions": [],
            "sectionActions": [],
            "timerActions":[]
        }
    }
}
```
