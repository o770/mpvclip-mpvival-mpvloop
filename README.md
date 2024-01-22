# MPVLOOP & MPVCLIP
MPVLOOP and MPVCLIP are bash scripts that execute MPV with playback options for loops, start and end times set according to a pattern in file names, a playlist or the command line.

## Usage
```
mpvloop {[<mpv profile,...>] [<loop pattern> | --ll] [path/]filename | --lpat <timestamp range>... | [--help | --version]}
mpvclip {[<mpv profile,...>] [<loop pattern> | --ll] [path/]filename | --lpat <timestamp range>... | [--help | --version]}
```

## Description
MPVLOOP runs MPV with any combination of the options: --start, --ab-loop-a, --ab-loop-b, --loop-file.

MPVCLIP runs MPV with any combination or none of the options: --start, --end.

MPV profile names are parsed and MPV is run according to: --profile.

MPV is only invoked to play a file with a loop pattern and is executed once for each loop pattern; otherwise the files are skipped. Input directories are expanded recursively. The line is read from left to right. If a key is pressed within one second before MPV runs again, the script may exit or continue after confirmation.

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
MPV profile names and a single loop pattern without flags can be entered on the command line. Options also include playlists and time format conversions.
Descriptions and examples in the Wiki.

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
mpvloopf {[<mpv profile,...>] <tag> [--ll] pathname | [--help | --version]}
mpvclipf {[<mpv profile,...>] <tag> [--ll] pathname | [--help | --version]}
```

## Description
Once for each tagged clip/loop pattern associated with a file and an optional MPV profile:

MPVCLIPF executes MPVCLIP; MPVLOOPF executes MPVLOOP; the file plays according to the tagged pattern.

Entered directories are searched recursively. The line is read from left to right. If a key is pressed within one second before MPVCLIP or MPVLOOP is executed again, the script may exit or continue after confirmation.

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
MPV profile names and a single tag without flags can be entered on the command line. Input directories or a playlist are searched.
Descriptions and examples in the Wiki.

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

