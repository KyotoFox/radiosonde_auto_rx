# RS41 demodulation chain

## Narrowband FM demodulation
```
rtl_fm -M raw -F9 -p 0 -d 0 -s 48000 -f 403000000 - 2>/dev/null

rtl_fm 
    -M raw          raw mode outputs 2x16 bit IQ pairs
    -F9             fir_size: enables low-leakage downsample filter (can be 0 or 9)
    -p 0            ppm_error
    -d 0            device_index
    -s 48000        sample_rate
    -f 403000000    frequency_to_tune_to
    - 
    2>/dev/null

STDOUT: IQ samples
```

## Remove DC offset
```
STIN: IQ samples

./iq_dec --bo 16 - 48000 16 2>/dev/null

./iq_dec 
    --bo 16         output bits per sample (signed16)
    - 
    48000           sr: sample rate
    16              bs: bits/sample
    2>/dev/null

STDOUT: filtered IQ samples?
```

## FSK Demodulator
```
./fsk_demod --cs16 -b -10000 -u 10000 -s --stats=5 2 48000 4800 - -

./fsk_demod 
    --cs16      The raw input file will be in complex signed 16 bit format.
    -b -10000   fsk_lower: lower limit of freq estimator (default 0 for real input, -Fs/2  for complex input)
    -u 10000    fsk_upper: upper limit of freq estimator (default Fs/2)
    -s          The output file will be in a soft-decision format, with one 32-bit float per bit.
    --stats=5   Print out modem statistics to stderr in JSON. 5 modem frames between printouts
    2           Mode 2FSK
    48000       Sample rate
    4800        Symbol rate
    -           Input modem raw file (IQ samples) - stdin
    -           Output file - stdout

SDERR: FSK Demod stats
```


## RS41 Decoder
./rs41mod --ptu2 --json --jsnsubfrm1 --softin -i