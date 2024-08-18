# MPVLOOP & MPVCLIP
MPVLOOP and MPVCLIP are bash scripts that execute MPV with playback options for loops, start and end times set according to a pattern in file names, a playlist or the command line.

## Usage
```
mpvloop {[<mpv profile,...>] [<loop pattern>... | -l] [-s] [path/]filename | --lpat <timestamp range>... | --help | --version}
mpvclip {[<mpv profile,...>] [<clip pattern>... | -l] [-s] [path/]filename | --lpat <timestamp range>... | --help | --version}
```

## Description
MPVLOOP runs MPV with any combination of the options: --start, --ab-loop-a, --ab-loop-b, --loop-file.

MPVCLIP runs MPV with any combination or none of the options: --start, --end.

MPV profile names are parsed and MPV is run according to: --profile.

MPV is only invoked to play a file with a loop pattern and is executed once for each loop pattern; otherwise the files are skipped. Input directories are expanded recursively. The line is read from left to right. If a key is pressed within one second before MPV runs again, the script may exit, continue, or skip the file after confirmation.

Status messages report clip patterns and MPV options used, clip duration, invalid inputs such as a file name without a pattern, and the number of successful iterations.

Except for time options and informational messages, both scripts share the same code and run independently of each other.

## Clip/Loop Pattern
Recognized patterns are formed with an initial letter A followed by numbers next to an intermediate letter B followed by numbers.

Letters are matched regardless of the case. Numbers are positive integers or zero and represent time in deciseconds, that is, tenths of a second. Zero disables each time point.

Patterns are converted to seconds with one decimal place and translated into time settings for loop playback from points A to B in MPVLOOP or playback start and end points in MPVCLIP.

| Pattern | Timestamp | Seconds |
| --- | --- | --- |
| a1b2 | from 00:00:00.1 to 00:00:00.2 | from 0.1 seconds to 0.2 seconds |
| a10b20 | from 00:00:01 to 00:00:02 | from 1 second to 2 seconds |
| A1234B5678 | from 00:02:03.4 to 00:09:27.8 | from 123.4 seconds to 567.8 seconds |
| a0b123 | from 00:00:00 to 00:00:12.3 | from start of file to 12.3 seconds |
| a123b0 | from 00:00:12.3 | from 12.3 seconds to end of file |
| a0b0 | from 00:00:00 | from the beginning to the end of the file |

<details>
<summary>Examples</summary>
	
For MPVLOOP command line:
```
$ mpvloop foo-a1b2-a10b20.mp4
```
How MPV is run:
```
$ mpv --start=0.1 --ab-loop-a=0.1 --ab-loop-b=0.2 foo-a1b2-a10b20.mp4 ; \
mpv --start=1 --ab-loop-a=1 --ab-loop-b=2 foo-a1b2-a10b20.mp4
```

For MPVCLIP command line:
```
$ mpvclip foo-a1b2-a10b20.mp4
```
How MPV is run:
```
$ mpv --start=0.1 --end=0.2 foo-a1b2-a10b20.mp4 ; \
mpv --start=1 --end=2 foo-a1b2-a10b20.mp4
```

</details>

## Options
MPV profile names and any number of loop patterns without flags can be entered on the command line. Options also include playlists and time format conversions. Descriptions and examples in the Wiki.

## Dependencies
- Awk
- file (command)
- GNU Bourne-Again SHell (Bash)
- GNU Coreutils
- GNU Grep
- mpv

# MPVLOOPF & MPVCLIPF
MPVLOOPF and MPVCLIPF are bash scripts that run MPVLOOP and MPVCLIP to play tagged loops and clips in a playlist or directory tree.

## Usage
```
mpvloopf {[<mpv profile,...>] <tag> [-p <pattern> | -l] [-s] [path/]filename | --help | --version}
mpvclipf {[<mpv profile,...>] <tag> [-p <pattern> | -l] [-s] [path/]filename | --help | --version}
```

## Description
Once for each tagged clip/loop pattern associated with a file and optionally a path pattern and MPV profile:

MPVCLIPF executes MPVCLIP; MPVLOOPF executes MPVLOOP; the file plays according to the tagged pattern.

Entered directories are searched recursively. The line is read from left to right. If a key is pressed within one second before MPVCLIP or MPVLOOP is executed again, the script may exit, continue, or skip the file after confirmation.

Status messages report the specified tag, invalid inputs, and the number of successful iterations. Both scripts run independently of each other.

## Tagged Clip/Loop Pattern
A tag is defined if a match to a regular expression specified on the command line is found at the end of a clip/loop pattern in file names or a playlist.

<details>
<summary>Examples</summary>
	
For MPVLOOPF command line:
```
$ mpvloopf Earth path/to
```
How MPVLOOP is run:
```
$ mpvloop a12b34 path/to/foo-a12b34Earth-a23b45Mars.mp4
```

For MPVCLIPF command line:
```
$ mpvclipf Earth path/to
```
How MPVCLIP is run:
```
$ mpvclip a12b34 path/to/foo-a12b34Earth-a23b45Mars.mp4
```

</details>

## Options
MPV profile names and a single tag without flags can be entered on the command line. Input directories or a playlist are searched. Descriptions and examples in the Wiki.

## Dependencies
- file (command)
- GNU Bourne-Again SHell (Bash)
- GNU Coreutils
- GNU Findutils (find)
- GNU Grep
- mpv

One or the other in a $PATH location:
- mpvloop (MPVLOOPF)
- mpvclip (MPVCLIPF)

# MPVIVAL
MPVIVAL is a bash script that runs MPV with the start and length of playback set based on intervals of the total duration of a file.

## Usage
```
mpvival {[<mpv profile,...>] <clip duration> <interval scale> [-l|-s|-t] [path/]filename | --help | --version}
```

## Description
MPV is run with the options: --start, --length.

MPV profile names are parsed and MPV is run according to: --profile.

MPV is only invoked once for each interval of the total duration of a file. The playback start time is random and moves forward in the stream without repeating. Intervals are calculated using a scale. The clip duration is reset to the interval length if it is longer than the interval.

Input directories are expanded recursively. If a key is pressed within 1 second before MPV runs again, the script may exit, continue, or skip the file after confirmation.

Status messages report clip duration, number of intervals, successful iterations, and MPV options used.

## Interval Scale
Intervals are scaled based on a range of 1 to 5, where 5 divides 1 hour into 60 one-minute intervals and 1 does not divide 1 minute.

12 seconds is the longest undivided duration. More than 1 hour, the duration is divided into intervals of 5, 4, 3, 2 or 1 minutes for the scale of 1, 2, 3, 4 or 5 respectively.

Number of intervals per scale and longest duration between 60 and 3600 seconds:
| Duration/Scale | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- |
| 60 | 1 | 2 | 3 | 4 | 5 |
| 327 | 2 | 4 | 6 | 8 | 10 |
| 654 | 3 | 6 | 9 | 12 | 15 |
| 981 | 4 | 8 | 12 | 16 | 20 |
| 1309 | 5 | 10 | 15 | 20 | 25 |
| 1636 | 6 | 12 | 18 | 24 | 30 |
| 1963 | 7 | 14 | 21 | 28 | 35 |
| 2290 | 8 | 16 | 24 | 32 | 40 |
| 2618 | 9 | 18 | 27 | 36 | 45 |
| 2945 | 10 | 20 | 30 | 40 | 50 |
| 3272 | 11 | 22 | 33 | 44 | 55 |
| 3600 | 12 | 24 | 36 | 48 | 60 |

<details>
<summary>Examples</summary>

For a 1 minute video, clip duration of 2 seconds, and interval scale 3:
```
$ mpvival 2 3 foo.mp4
```
How MPV is run a first and second time, showing a random start time:
```
$ mpv --start=7 --length=2 foo.mp4 ; \
mpv --start=20 --length=2 foo.mp4 ; \
mpv --start=48 --length=2 foo.mp4
```
```
$ mpv --start=18 --length=2 foo.mp4 ; \
mpv --start=37 --length=2 foo.mp4 ; \
mpv --start=41 --length=2 foo.mp4
```

</details>

## Options
The interval scale number is followed by files unless a playlist flag is entered on the command line, which must follow the scale. Descriptions and examples in the Wiki.

## Dependencies
- FFmpeg (ffprobe)
- file (command)
- GNU Bourne-Again SHell (Bash)
- GNU Coreutils
- GNU Grep
- mpv

