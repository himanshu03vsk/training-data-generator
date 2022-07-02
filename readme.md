# Training data generator

This is a handy tool that converts the youtube videos to audio along with their subtitles to produce training data for DL models


usage:<br>
`python data_generator.py --csv data.csv --o training --i_audio audio --i_subs subs --sr 48000 --format mp4`<br>
<br>
Install requirements from `requirements.txt`<br>
You can use `data_generator.py -h` for help and seeing the list of arguments that it takes<br>
# Important
***<p style="color: #f00">Make sure that you only provide folder names that are present or will be created in current working directory.</p>***<br>
***<p style="color: #f00">`--format` flag represents in what format the video file that only has audio will be downloaded not the audio format that you wish to download in. By default the splitted audio is in `.wav` form.</p>***

You may encounter error of something like this: <br>
`KeyError                                  Traceback (most recent call last)
~\test_pytube.py in <module>
     10 for v in p.videos[:3]:
     11     print("trying to get captions for:", v.title)
---> 12     print(v.captions["a.en"].generate_srt_captions())

~\AppData\Roaming\Python\Python38\site-packages\pytube\captions.py in generate_srt_captions(s
elf)
     49         recompiles them into the "SubRip Subtitle" format.
     50         """
---> 51         return self.xml_caption_to_srt(self.xml_captions)
     52
     53     @staticmethod

~\AppData\Roaming\Python\Python38\site-packages\pytube\captions.py in xml_caption_to_srt(self
, xml_captions)
     81             except KeyError:
     82                 duration = 0.0
---> 83             start = float(child.attrib["start"])
     84             end = start + duration
     85             sequence_number = i + 1  # convert from 0-indexed to 1.

KeyError: 'start'`

<br>
To fix this, replace the `xml_caption_to_srt()` method of  `catpions.py` file in `pysrt` with below code<br>
`    def xml_caption_to_srt(self, xml_captions: str) -> str:
        """Convert xml caption tracks to "SubRip Subtitle (srt)".

        :param str xml_captions:
        XML formatted caption tracks.
        """
        segments = []
        root = ElementTree.fromstring(xml_captions)
        i=0
        for child in list(root.iter("body"))[0]:
            if child.tag == 'p':
                caption = ''
                if len(list(child))==0:
                    # instead of 'continue'
                    caption = child.text
                for s in list(child):
                    if s.tag == 's':
                        caption += ' ' + s.text
                caption = unescape(caption.replace("\n", " ").replace("  ", " "),)
                try:
                    duration = float(child.attrib["d"])/1000.0
                except KeyError:
                    duration = 0.0
                start = float(child.attrib["t"])/1000.0
                end = start + duration
                sequence_number = i + 1  # convert from 0-indexed to 1.
                line = "{seq}\n{start} --> {end}\n{text}\n".format(
                    seq=sequence_number,
                    start=self.float_to_srt_time_format(start),
                    end=self.float_to_srt_time_format(end),
                    text=caption,
                )
                segments.append(line)
                i += 1
        return "\n".join(segments).strip()`
