# ffmpegGif
A repository featuring ffmpeg commands to convert videos into GIFs

For the purposes of this tutorial, I will be using example paths to my files. Replace these with the relevant paths to your files when you repeat them

## Basic GIF conversion

The most basic method with which we can convert a video file to a GIF is with the command

```ffmpeg -i C:/ffmpeg/wildlife.mkv C:/ffmpeg/output.gif```

The `-i` flag means that the next item that we pass is the path to an input file, in this example, I’m passing `C:/ffmpeg/wildlife.mkv` as an input file.
After this, we see an extra path at the end of the command. This is the output path, the output path will always be the last thing featured in all of the commands we run. We don't need to do anything special to signify that this is the output path, simply having it at the end of the command is enough.

You may notice that little to no information about the change I want to take place has been given in the command, this is because ffmpeg will implicitly convert my input file to the format of my output file without having to specify. (note how the output path ends with `.gif`)

Being the most basic method of converting a video to a GIF, the command is very simple to understand but isn't very useful. What if we only wanted to turn a certain part of a video into a GIF?

## Cropping a section of a video to a GIF

ffmpeg allows us to specify the section of the video we want to crop, we can specify the start with the `-ss` flag (followed by a time) and mark the end with the `-to` flag (followed by a time)

```ffmpeg -i C:/ffmpeg/wildlife.mkv -ss 00:10 -to 00:15 C:/ffmpeg/output.gif```

Much better, now we have a 5 second GIF sampled from 10 seconds into the video until 15 seconds

However, due to the GIF spec, this file 5-second clip is already much larger than the original 30-second video!
Let's cut down the unnecessary resolution of this file to lower the filesize.

## Resizing the input to create a smaller GIF
This is where things get a little more complicated, we're going to apply our first filter to `scale` the input before we turn it into a gif

```ffmpeg -i C:/ffmpeg/wildlife.mkv -ss 00:10 -to 00:15 -vf scale=340:-1:lanczos C:/ffmpeg/output.gif```

In this command, we have introduced the `-vf` flag. This flag stands for "video filters" and should be followed by what ffmpeg calls a "chain" of filters.
The addition may seem complicated but let's break it down `-vf scale=340:-1:lanczos`
the first thing after the `-vf` flag is the word `scale`, this is the filter we're applying. `=` the equals sign indicates that we're passing some extra parameters to the `scale` filter, the first of which `340` is the width we want to scale our GIF to (ffmpeg will automatically decide the height to maintain the aspect ratio).
After this, we have a `:` this just acts as a separator between the arguments to scale. after this, `-1` this is an argument related to the interlacing of the source video, don't worry about this one. finally, we have `lanczos` this is the method that will be used for rescaling our video, again, don't worry about this too much.

## Creating a custom colour palette for our GIF
This step can lead to a real increase in quality, however, it is much more complicated than the other commands we've looked at so far.

Since the GIF format is limited to 256 colours but our videos likely contain far more colour variation than this, we need to make the best use of these colours that we can. If we don't specify any special palette to use, ffmpeg will use a generalised colour palette that tries to be applicable to every video. It looks like this

![ffmpeg colour palette](https://i.stack.imgur.com/SwWOM.png)

These colours might not be the most suitable for our video, so we generate one that is suitable!

```ffmpeg  -i C:/ffmpeg/wildlife.mkv -ss 00:10 -to 00:15 -vf [0]scale=320:-1:flags=lanczos,split[s0][s1];[s0]palettegen=[p];[s1][p]paletteuse   C:/ffmpeg/output.gif```

The video filters we're applying in this example are much more complex and harder to follow, they make use of labels and use the split command to complicate the process even further. I won't be explaining how this works as part of this tutorial, but the GIF generated will have a more specific colour palette and should be noticeably higher quality!
