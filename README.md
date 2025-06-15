# ffmpeg-examples
Just some ffmpeg usage I've written down

## game frame sequence to prores:
`ffmpeg -framerate 60 -i hat_movie13_%04d.tga -threads 8 -c:v prores_ks -profile:v 3 -vendor ap10 -pix_fmt yuv422p10le D:\hat_movie13_prores1.mov`

## extract audio:
`ffmpeg -i denzel.mp4 -vn -acodec copy denzel.aac`
`ffmpeg -i S05E08.mkv -vn -c:a pcm_f32le -ar 48000 s05e08.wav`

## possible video profile (-profile:v) values for prores_ks:
```
5 = 4444 XQ
4 = 4444
3 = HQ (high quality)
2 = standard (sometimes called ST)
1 = LT (light)
0 = proxy (sometimes called PR)
```

## convert to prores:
`ffmpeg -i MVI_0011.MOV -c:v prores_ks -profile:v 3 -vendor ap10 -pix_fmt yuv422p10le prores_0011.mov`

## fast convert to prores - lower q:v gives larger frames with much more speed:
`ffmpeg -i S05E07.mkv -c:v prores_ks -profile:v 2 -c:a copy -pix_fmt yuv422p10 -vendor ap10 -q:v 5 s05e07.mov`

## fast convert to prores - lower q:v gives larger frames with much more speed - with wav audio:
`ffmpeg -i S05E07.mkv -c:v prores_ks -profile:v 2 -c:a pcm_f32le -ar 48000 -pix_fmt yuv422p10 -vendor ap10 -q:v 5 s05e07.mov`

## fast src to mov for non-linear editors:
`ffmpeg -i "ep06.mkv" -c:v prores_ks -profile:v 3 -c:a pcm_f32le -ar 48000 -pix_fmt yuv422p10 -vendor ap10 -q:v 5 -sn "N:\clipstemp\ff06.mov"`

## layer alpha video over color + to h264:
`ffmpeg -f lavfi -i color=c=black:s=1280x720:r=30 -i 2020-playsstuff1.mov -filter_complex "[0:v][1:v]overlay=shortest=1,format=yuv420p[out]" -map "[out]" -c:v libx264 -profile:v baseline -c:a none -pix_fmt yuv420p logo.mp4`

## to 720p h264:
`ffmpeg -i hat_s1_comp.mxf -c:v libx264 -profile:v baseline -c:a aac -pix_fmt yuv420p -vf scale=-1:720 -ss 00:01.267 -to 09:21 hat_s1_enc720p_out.mp4`

## burn subtitles + to h264:
`ffmpeg -i "sub04.mkv" -c:v libx264 -profile:v baseline -c:a pcm_f32le -ar 48000 -pix_fmt yuv420p -vf subtitles=sub04.mkv -ss 04:05 -to 06:30 out_04.mov`

## burn subtitles + to h264 cbr & aac:
`ffmpeg -i "11.mkv" -c:v libx264 -b:v 1800k -c:a aac -pix_fmt yuv420p -vf subtitles=11.mkv -ss 09:36 -to 15:18 11_2.mp4`

## burn subtitles + to h264 + choosing streams:
(map 0:0 -> video#0, map 0:2 -> audio#2, subtitles=X:si=1 -> subs#1)  
`ffmpeg -i "diablo06.mkv" -c:v libx264 -profile:v baseline -c:a:2 pcm_f32le -ar 48000 -pix_fmt yuv420p -vf subtitles=diablo06.mkv:si=1 -ss 08:36 -to 08:48 -map 0:0 -map 0:2 out_diablo06.mov`

## bluray copy:
`ffmpeg -i "I:\BDMV\STREAM\00007.m2ts" -map 0 -acodec pcm_s24be -vcodec copy -scodec copy AICL_3537-9_KOD_00007.mkv`

## bluray rip for edit:
`ffmpeg -i "F:\BDMV\STREAM\00000.m2ts" -map 0:1 -c:a pcm_s24le 01_01_main.wav`
`ffmpeg -i "F:\BDMV\STREAM\00000.m2ts" -map 0:2 -c:a pcm_s24le 01_01_commentary.wav`
`ffmpeg -i "F:\BDMV\STREAM\00000.m2ts" -map 0:0 -an -sn -dn -threads 8 -c:v prores_ks -profile:v 3 -q:v 5 -pix_fmt yuv422p10le -vendor apl0 01_01_video.mov`

## burn subtitles + select audio + to prores:
`ffmpeg -i class20.mkv -map 0:0 -map 0:2 -c:v prores_ks -profile:v 2 -c:a pcm_f32le -ar 48000 -pix_fmt yuv422p10 -vendor ap10 -q:v 5 -vf subtitles=class20.mkv:si=1 -ss 08:43 -to 17:55 class20_out.mov`

## burn subtitles + to 720p h264/aac + mp4:
`ffmpeg -i "ff05.mkv" -c:v libx264 -profile:v baseline -c:a aac -pix_fmt yuv420p -vf "scale=-1:720, subtitles=ff05.mkv" -ss 08:06 -to 08:58 out.mp4`

## merge video + new audio:
`ffmpeg -i main_seq.mp4 -i shit.m4a -c:v copy -c:a copy -map 0:v:0 -map 1:a:0 -shortest out___.mp4`

## rip from bluray Rec709 to PNG sequence (0-255) in MOV + PCM audio:
`ffmpeg -i "original.m2ts" -v verbose -acodec pcm_f32le -vcodec png -pix_fmt rgb24 -src_range 0 -dst_range 1 -vf "setparams=range=limited:color_primaries=bt709:color_trc=bt709:colorspace=bt709,scale=in_range=mpeg:in_color_matrix=bt709:out_range=full:flags=spline+full_chroma_inp+accurate_rnd+full_chroma_int+full_chroma_inp+print_info" -sws_dither x_dither -color_primaries bt709 -color_trc bt709 -colorspace bt709 mmm_original.mov`
