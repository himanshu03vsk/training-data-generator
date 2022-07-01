# Training data generator

This is a handy tool that converts the youtube videos to audio along with their subtitles to produce training data for DL models


usage:<br>
`python data_generator.py --csv data.csv --o training --i_audio audio --i_subs subs --sr 48000 --format mp4`<br>
<br>
Install requirements from `requirements.txt`<br>
You can use `data_generator.py -h` for help and seeing the list of arguments that it takes<br>
# Important
***<p style="color: #f00">Make sure that you only provide folder names that are present in current working directory.</p>***<br>
***<p style="color: #f00">`--format` flag represents in what format the video file that only has audio will be downloaded not the audio format that you wish to download in. By default the splitted audio is in `.wav` form.</p>***