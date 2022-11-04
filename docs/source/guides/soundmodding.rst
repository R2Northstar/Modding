Sound Modding
==============


Audio Type
-----------

-  You will need to use ``WAV`` format and either ``48000hz`` or ``44100hz`` sample rate.
-  More details below in `creating_your_sound`_

Step 1 - Identifying the Sound
-------------------------------

First you need to identify the exact sound. There's a command for this:
``ns_print_played_sounds 1`` It will show all the audio events that
are happening at that moment on the console.

For example, use your Grapple and open the console the event ID will be
``pilot_grapple_fire``

How it looks in the console: |console|

All weapons, boosts, tacticals have different events IDs on different surfaces (concrete, solidmetal, wood, dirt etc.)
That's why you must identify the exact event/s. Examples based on Grapple:

-  Concrete impact: ``concrete_grapple_impact_1p_vs_3p``
-  Solidmetal impact: ``solidmetal_grapple_extract_1p_vs_3p`` etc.


**NOTE:** ``ns_print_played_sounds 1`` will show you every event ID. Not
just in-match ones. For example:

- The sound when you move the cursor to an option it will be ``menu_focus``, and clicking sound will be ``menu_accept`` or menu music ``mainmenu_music``

Check the console often, as it's easy to miss your sound - there can be a lot of sounds playing.

There's also a `list <https://gist.github.com/begin-theadventure/84c46e803aa358b102d754ff992ae9e4>`_ of all EvenIDs (audio).

Sounds can be played in-game from the console via ``script_ui EmitUISound(soundname)``.

Additionally `subtitles <https://gist.github.com/begin-theadventure/cf941af91cd158de4fde747ec78c2902>`_ in the form of plain text can also be useful.

You can also export sounds with `LegionPlus <https://github.com/r-ex/LegionPlus>`_ instead of playing them in-game.

Open LegionPlus.exe -> Load File -> Titanfall 2/r2/sound pick general.mbnk

If you want to export only a specific sound use search. It's possible to export selected sounds and all (after a search it exports only the found assets).

Step 2 - Creating Folders
--------------------------

When you successfully identified your event and have the audio file/s
ready it's time to set up the folder structure.
Assuming the event name is ``pilot_grapple_fire``, the folder structure of your mod should look like this:

.. code-block::
   
   author.mod/
   ├── audio/
   │   ├── pilot_grapple_fire/
   │   │   └── my_audio.wav
   │   └── pilot_grapple_fire.json
   └── mod.json
   

Example of a ``mod.json`` (documented here: :doc:`/guides/gettingstarted`)


.. code-block:: json

   {
     "Name": "MOD_NAME_HERE",
     "Description": "DESCRIPTION_HERE",
     "Version": "0.1.0",
     "LoadPriority": 2
   }


**Inside the ``audio/`` folder:**

-  ``pilot_grapple_fire/`` folder which needs to contain your .wav file(s)
-  ``pilot_grapple_fire.json`` json used to configure the sound override, dont forget to edit.

You will have to add that folder with your sound and the json for each event you want to override.
The event JSON files must contain both ``EventId`` and ``AudioSelectionStrategy`` like this:


.. code-block:: json

   {
       "EventId": [ "pilot_grapple_fire" ],
       "AudioSelectionStrategy": "sequential"
   }

The ``AudioSelectionStrategy`` can be either:

- ``sequential``: If you have one sound or you want to play them in alphabetical order.
- ``random``: If you have more than one sound and you want to randomize them.


.. _creating_your_sound:

Creating Your Sound
--------------------
- **Recommended tool:** `Audacity <https://www.audacityteam.org/download/>`_

Open/Add your audio as a track to Audacity and set your sample rate to
either ``48000hz`` or ``44100hz``. In Audacity, select your entire track, open
the effects dropdown menu, then click ``Change Speed``. In the dialog set
either ``Speed Multiplier`` to ``0.918`` or ``Percent Change`` to ``-8,200``. After
that, export your track as ``wav`` and make sure you don't add any
metadata.

- White noise after the sound ends.

This is usually because there's some metadata left in the audio.

.. tabs::

   .. tab:: Windows

      You can bulk remove it with `Mp3tag <https://www.mp3tag.de/en/download.html>`_ or individually with Audacity.

   .. tab:: Linux

      You can bulk remove it with `Metadata Cleaner <https://metadatacleaner.romainvigier.fr>`_ or a shell script (requires ffmpeg to be installed) and also individually with Audacity.

      ``metadata_remover.sh`` (WAV only)

      .. tabs::

         .. code-tab:: shell Script

            shopt -s globstar nullglob
            for f in *.wav **/*.wav
            do
              ffmpeg -i "$f" -map 0 -map_metadata -1 -c:v copy -c:a copy "${f%.wav}.new.wav"
              mv -f "${f%.wav}.new.wav" "$f"
            done

- Converters

.. tabs::

   .. tab:: Windows

      # todo

   .. tab:: Linux

      On Linux you can use shell scripts that convert all WAV or MP3 audio files from the current directory (including folders) to WAV 48000Hz 16-bit. They require ffmpeg to be installed.

      MP3 and other formats scripts don't delete previous files, so just search for them (.format) and delete after conversion. WAV script automatically replaces old files.

      .. tabs::

         .. code-tab:: shell WAV script
            
            #WAV to WAV 16-bit 48000 Hz.
            #wav_converter.sh
            
            shopt -s globstar nullglob
            for f in *.wav **/*.wav
            do
              ffmpeg -i "$f" -acodec pcm_s16le -ar 48000 "${f%.wav}.new.wav"
              mv -f "${f%.wav}.new.wav" "$f"
            done

      .. tabs::

         .. code-tab:: shell MP3 Script
            
            #MP3 to WAV 16-bit 48000 Hz.
            #mp3-wav_converter.sh
            
            shopt -s globstar nullglob
            for f in *.mp3
            do
              ffmpeg -i "${f}" -vn -c:a pcm_s16le  -ar 48000 "${f%.*}.wav"
            done

      .. tabs::

         .. code-tab:: shell Script for other formats
            
            #Replace .format with the one you want to convert.
            #format-wav_converter.sh
            
            shopt -s globstar nullglob
            for f in *.format
            do
              ffmpeg -i "${f}" -vn -c:a pcm_s16le  -ar 48000 "${f%.*}.wav"
            done

Installation
-------------
-  As with any mod, the folder which contains your ``mod.json`` needs to be inside ``Titanfall 2/r2Northstar/Mods/``.

.. |console| image:: https://raw.githubusercontent.com/rwynx/audio-overriding-northstar/main/Images/audioeventeample.png
