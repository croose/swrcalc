# swrcalc

Last year at [Ham Radio University](http://hamradiouniversity.org/), [Mike WY2U](https://www.qrz.com/db/wy2u) gave an excellent presentation on [Antenna Power Flow and SWR](http://hamradiouniversity.org/wp-content/uploads/2020/01/power_flow6.pptx) (PowerPoint). It was heavy on math, but clarified some things for me about calculating and understanding the efficiency of antenna systems and how antenna tuners work that I hadn't run across in the [ARRL Handbook](http://www.arrl.org/shop/ARRL-Handbook/) or [Antenna Book](http://www.arrl.org/shop/ARRL-Antenna-Book/).

Inspired by the [spreadsheet](http://hamradiouniversity.org/wp-content/uploads/2020/01/VSWR_Loss_13b.xlsx) (Excel) he used to demonstrate these concepts during his talk, I developed [a similar tool](https://git.io/JTE5C) that can be used at the command line:

    % swrcalc -h
    usage: swrcalc [-h] [-g GAIN] -s SWR [-t LOSS] [-a LOSS] [-n LOSS]
    
    Calculate antenna system efficiency, tuner stress, and effective gain
    
    optional arguments:
      -h, --help            show this help message and exit
      -g GAIN, --gain GAIN  antenna gain (dBi)
    
    required:
      -s SWR, --swr SWR     SWR at xmtr
    
    if using tuner:
      -t LOSS, --xmtr-tuner LOSS
                            loss between output of xmtr and output of tuner (dB)
      -a LOSS, --tuner-ant LOSS
                            loss between output of tuner and input of antenna (dB)
    
    without tuner:
      -n LOSS, --no-tuner LOSS
                            loss between output of xmtr and input of antenna (dB)

An example based on a non-resonant doublet and [75' of RG-8X](https://www.timesmicrowave.com/Calculator?Product=RG-8X&RunLength=75&Frequency=14.23) (1.1 dB insertion loss @ 14.23 MHz), tuned to 1.5 SWR, with 0.5 dB loss in the tuner:

    % swrcalc --swr 1.5 --gain 2.15 --xmtr-tuner .5 --tuner-ant 1.1
    
    Antenna system efficiency: 67%
    Maximum power at tuner: 0.94x
    Effective antenna gain: 0.40 dBi

Connecting that same 75' length of RG-8X to a resonant dipole on 20m with identical SWR:

    % swrcalc --swr 1.5 --gain 2.15 --no-tuner 1.1
    
    Antenna system efficiency: 72%
    Effective antenna gain: 0.75 dBi

Maybe you're happy with your multi-band doublet, but you want to see what would happen if the tuner was at the far end of the transmission line, directly connected to the antenna feed point:

    % swrcalc --swr 1.5 --gain 2.15 --xmtr-tuner 1.6
    
    Antenna system efficiency: 69%
    Maximum power at tuner: 0.75x
    Effective antenna gain: 0.55 dBi

And if you ever questioned the need to turn down your power when tuning up...

    % swrcalc --swr 10 --xmtr-tuner .7
    
    Antenna system efficiency: 85%
    Maximum power at tuner: 11.21x

In the above example, if you see a 10:1 SWR at your rig and tune up at 100 W (with the tuner at antenna end of transmission line), your tuner will see 1.1 kW of combined forward and reflected power! (The SWR at the antenna feed point is 50.65!) But notice that, if your tuner can handle the power, the whole system is still 85% efficient -- 85 watts is being delivered to the antenna.

If you turn the power down to 5 W to tune up, then the tuner is much less stressed, only seeing 56 W of combined forward and reflected power. And assuming it tunes to 1.5 SWR, you've still got an 85% efficient system:

    % swrcalc --swr 1.5 --xmtr-tuner .7
    
    Antenna system efficiency: 85%
    Maximum power at tuner: 0.90x

So those are a few examples to demonstrate how to use the tool. You can download the Python script [here](https://git.io/JTE5C). And check out WY2U's slides (linked above) for a more thorough explanation of antenna system dynamics.
