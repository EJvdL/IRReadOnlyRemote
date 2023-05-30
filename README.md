# IRReadOnlyRemote
 Private changes to the existing Arduino IRReadOnlyRemote library

 URL of original repository: https://github.com/otryti/IRReadOnlyRemote.git

**IR-Code coding**

 Mark  : IR-LED is on<br>
 SPACE : IR-LED is off
 
 An IR-code is coded as follows:
 * Start of frame<br>
 	16 * 562us (9ms)   Mark<br>
  	 8 * 562us (4.5ms) Space<br>
 * 32 bits data <br>
 	1 is coded as: 562us Mark + 1 * 562us Space<br>
    0 is coded as: 562us Mark + 3 * 562us Space<br>
 * End of frame<br>
 	562us Mark<br>
 
 A repeat command is coded as<br>
 *	16 * 562us (9ms)   Mark<br>
 * 	4 * 562us (2.25ms) Space<br>
 *	 1 * 562us Mark<br>
 
This is the **original** timing with ±factor 1,25<br>
Repeat code lower threshold has overlap with 3*562us<br>
 |bit time (*562us)|Lower  | Upper
 |:---:            | :---:   | :---:|
 | 16	             |7219     | 11280
 |  8	             |3610     | 5640
 |  4	             |**1805** |	2820
 |  3	             |1354     | **2115**
 |  1	             |451      | 705

**Improved timing**<br>
 |bit time (*562us)	|    Lower	 | Upper
 |:---:            | :---:   | :---:|
 |  16	            |    7219	    | 11280
 |   8	            |    3610	    | 5640
 |   4	            |    **1905**	| **3000**
 |   3	            |    **1200** | **1900**
 |   1	            |    **300**  | **1000**

The library had to be adapted because of the timing parameters<br>
The following lines have been adapted:<br>
  
**Removed line**
`
  // duration += 100; // Changed EJ
`

**Changed lines**
```
            if (rptSpace > 0) {
              rptSpace *= baseTimeUnit;
              rptSpaceLow = 1905; // rptSpace / ALLOWED_LIMIT;  // Changed EJ
              rptSpaceHigh = 3000; // rptSpace * ALLOWED_LIMIT; // Changed EJ
            } else {
              rptSpaceLow = 0;
              rptSpaceHigh = 0;
            }
          
            bitMark *= baseTimeUnit;
            bitMarkLow = 300; //bitMark / (ALLOWED_LIMIT; // Changed EJ
            bitMarkHigh = 1000; bitMark * ALLOWED_LIMIT;  // Changed EJ
          
            zeroSpace *= baseTimeUnit;
            zeroSpaceLow = 300; //zeroSpace / ALLOWED_LIMIT;    // Changed EJ
            zeroSpaceHigh = 1000; //zeroSpace * ALLOWED_LIMIT;  // Changed EJ
          
            oneSpace *= baseTimeUnit;
            oneSpaceLow = 1200; //oneSpace / ALLOWED_LIMIT;     // Changed EJ
            oneSpaceHigh =  1900; //oneSpace * ALLOWED_LIMIT;   // Changed EJ
```
