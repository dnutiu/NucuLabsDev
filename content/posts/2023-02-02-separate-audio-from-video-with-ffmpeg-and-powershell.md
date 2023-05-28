---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Windows
date: '2023-02-02T22:41:38+00:00'
guid: https://nuculabs.dev/?p=2582
id: 2582
image: /wp-content/uploads/2023/02/image.png
layout: post
permalink: /2023/02/02/separate-audio-from-video-with-ffmpeg-and-powershell/
tags:
- audacity
- davinci resolve
- ffmpeg
- final cut pro
- noise gate
- noise removal
- powershell
- scripting
- video editing
- voice isolation
title: "Separate Audio from Video with\_ffmpeg and Powershell"
---
Hello 👋,


A while ago I wrote an article on how to separate audio from video with ffmpeg on Linux using bash. The article can be read here:


[Separate Audio from Video (with ffmpeg)](https://nuculabs.dev/2022/12/08/separate-audio-and-video/)
Now I want to introduce you to the Windows version with some little Powershell functions that I’ve wrote.


## But why?


If you’re a content creator and you are not using the free program [Audacity ](https://www.audacityteam.org/)for editing your audio files, I believe you’re missing out on some great features. In my opinion, the noise gate feature is very usefull for eliminating background sounds and breaths from your audio. My PC fan and keyboard strokes are pretty loud and I can easily eliminate them with these settings:


![](/wp-content/uploads/2023/02/image.png?w=1024)
I tested both the Final Cut Pro and Davinci’s Resolve voice isolation feature and I find that the Noise Gate from Audacity works best for me.


## My Workflow


I have an raw and unedited video which I record with OBS, then I split the audio by running:


```
 Split-Video .\InstallDockerAndDockerCompose.mkv
```


Then I open up the audio file InstallPythonAndPycharmTemp.aup3 in Audacity, apply my desired effects and save the file in the same directory with the name **InstallDockerAndDockerComposeTemp.wav**.


Then I run:


```
Join-Video .\InstallDockerAndDockerCompose.mkv
```


And it will give me the final video named **InstallDockerAndDockerComposeFinal.mp4** with the audio edited in Audacity! 😄


## Powershell Functions


You can add these functions to your PowerShell profile by typing **code $PROFILE**. It should open a Visual Studio Code instance and your PS profile file.


Then paste the following functions into the file:


```
function global:Split-Video {
  [CmdletBinding()]
  [Alias('vsplit')]
  param (
    [Parameter(Mandatory=$True,Position=0,ValueFromRemainingArguments=$False)]
    [String] $VideoPath
  )
  begin {
    $filename = Get-ChildItem -Path $VideoPath
    $filename_wo_extension = $filename.basename
  }
  process {
    ffmpeg -i "$VideoPath" -vn -c:a copy "${filename_wo_extension}Temp.m4a"
    ffmpeg -i "$VideoPath" -an -c:v copy "${filename_wo_extension}Temp.mp4"
  }
}


function global:Join-Video {
  [CmdletBinding()]
  [Alias('vjoin')]
  param (
    [Parameter(Mandatory=$True,Position=0,ValueFromRemainingArguments=$False)]
    [String] $VideoPath,
    [Parameter(Mandatory=$False,Position=1,ValueFromRemainingArguments=$False)]
    [String] $AudioPath
  )
  begin {
    $filename = Get-ChildItem -Path $VideoPath
    $filename_wo_extension = $filename.basename
    # Test AudioPath for Null
    if ($AudioPath -notmatch '\S') {
      $audio_file = "./${filename_wo_extension}Temp.wav"
    } else {
      $audio_file = $AudioPath
    }
  }
  process {
    ffmpeg -i "./${filename_wo_extension}Temp.mp4" -i "$audio_file" -c:v copy -c:a aac "./${filename_wo_extension}AudioFix.mp4"
    Remove-Item -Force "./${filename_wo_extension}Temp.mp4"
    Remove-Item -Force "./${filename_wo_extension}Temp.m4a"
  }
}
```


Ensure that you have **ffmpeg** installed. I’ve installed it with:


```
choco install ffmpeg
```


## Conclusion


Thanks for reading!


What do you think about this workflow, do you have any ideas how it can be improved?


Denis