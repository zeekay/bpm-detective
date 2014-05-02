#!/usr/bin/env python
import csv
from mutagen.easyid3 import EasyID3
import os
import subprocess
import sys
from StringIO import StringIO

basedir = os.path.dirname(os.path.abspath(__file__))

os.environ['VAMP_PATH'] = os.path.join(basedir, 'vamp-plugins')

for song in sys.argv[1:]:
    args = [
        '{0}/sonic-annotator'.format(basedir),
        # '-d',
        # 'vamp:qm-vamp-plugins:qm-tempotracker',
        # '-d',
        # 'vamp:qm-vamp-plugins:qm-keydetector',
        '-t',
        '{0}/tempo.n3'.format(basedir),
        song,
        '-w',
        'csv',
        '--csv-stdout'
    ]

    results = StringIO(subprocess.check_output(args, stderr=subprocess.PIPE))
    reader = csv.reader(results)
    tempos = []
    # keys   = []
    for row in reader:
        # if len(row) == 4:
        #     keys.append(row[-1])
        # elif len(row) == 3:
        tempos.append(float(row[-1].split(' ')[0]))

    print tempos

    # fudge down doubled bpm detections
    # min_bpm = min(tempos)
    # for i, bpm in enumerate(tempos):
    #     while bpm >= 100:
    #         bpm = float(bpm) / 2
    #     tempos[i] = bpm

    # calculate median
    tempos.sort()
    length = len(tempos)

    if not length % 2:
        tempo = int(round((tempos[length / 2] + tempos[length / 2 - 1]) / 2.0))
    else:
        tempo = int(round(tempos[len(tempos) / 2]))

    if tempo < 100:
        tempo = tempo * 2

    print os.path.basename(song)
    print '--------------------'
    print 'Detected BPM:', tempo

    id3 = EasyID3(song)
    try:
        bpm = id3['bpm']
        print 'Tagged BPM:', bpm
    except:
        pass

    try:
        length = id3['length']
        print 'Tagged Length:', length
    except:
        pass
