commit bc46696dc451e1815266fffbe592001965ad6597
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 20 09:31:53 2022 +0100

    unify license text

diff --git a/fpga-xc2s30/clk_divider.v b/fpga-xc2s30/clk_divider.v
index 9a57879b0..fbb3250f1 100644
--- a/fpga-xc2s30/clk_divider.v
+++ b/fpga-xc2s30/clk_divider.v
@@ -1,10 +1,19 @@
 //-----------------------------------------------------------------------------
-// Copyright (C) 2014 iZsh <izsh at fail0verflow.com>
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
 //
-// This code is licensed to you under the terms of the GNU GPL, version 2 or,
-// at your option, any later version. See the LICENSE.txt file for the text of
-// the license.
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
+
 module clk_divider(input clk, input [7:0] divisor, output [7:0] div_cnt, output div_clk);
 
     reg [7:0] div_cnt_ = 0;
diff --git a/fpga-xc2s30/fpga_felica.v b/fpga-xc2s30/fpga_felica.v
index 2cdc0bdbe..6fb53927c 100644
--- a/fpga-xc2s30/fpga_felica.v
+++ b/fpga-xc2s30/fpga_felica.v
@@ -1,4 +1,19 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+//
 // The FPGA is responsible for interfacing between the A/D, the coil drivers,
 // and the ARM. In the low-frequency modes it passes the data straight
 // through, so that the ARM gets raw A/D samples over the SSP. In the high-
@@ -9,10 +24,9 @@
 // could be improved.
 //
 // Jonathan Westhues, March 2006
-// Added ISO14443-A support by Gerhard de Koning Gans, April 2008
-// iZsh <izsh at fail0verflow.com>, June 2014
-// Piwi, Feb 2019
-//-----------------------------------------------------------------------------
+//
+// Added ISO14443-A support
+//
 
 
 // Defining commands, modes and options. This must be aligned to the definitions in fpgaloader.h
diff --git a/fpga-xc2s30/fpga_hf.v b/fpga-xc2s30/fpga_hf.v
index 684f38f70..8ee85e26b 100644
--- a/fpga-xc2s30/fpga_hf.v
+++ b/fpga-xc2s30/fpga_hf.v
@@ -1,4 +1,19 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+//
 // The FPGA is responsible for interfacing between the A/D, the coil drivers,
 // and the ARM. In the low-frequency modes it passes the data straight
 // through, so that the ARM gets raw A/D samples over the SSP. In the high-
@@ -9,10 +24,9 @@
 // could be improved.
 //
 // Jonathan Westhues, March 2006
-// Added ISO14443-A support by Gerhard de Koning Gans, April 2008
-// iZsh <izsh at fail0verflow.com>, June 2014
-// Piwi, Feb 2019
 //-----------------------------------------------------------------------------
+// Added ISO14443-A support
+//
 
 
 // Defining commands, modes and options. This must be aligned to the definitions in fpgaloader.h
diff --git a/fpga-xc2s30/fpga_hf_15.v b/fpga-xc2s30/fpga_hf_15.v
index d5c2138d6..b82ae7fc7 100644
--- a/fpga-xc2s30/fpga_hf_15.v
+++ b/fpga-xc2s30/fpga_hf_15.v
@@ -1,17 +1,24 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+//
 // The FPGA is responsible for interfacing between the A/D, the coil drivers,
 // and the ARM. In the low-frequency modes it passes the data straight
 // through, so that the ARM gets raw A/D samples over the SSP. In the high-
 // frequency modes, the FPGA might perform some demodulation first, to
 // reduce the amount of data that we must send to the ARM.
-//
-// I am not really an FPGA/ASIC designer, so I am sure that a lot of this
-// could be improved.
-//
-// Jonathan Westhues, March 2006
-// Added ISO14443-A support by Gerhard de Koning Gans, April 2008
-// iZsh <izsh at fail0verflow.com>, June 2014
-// Piwi, Feb 2019
 //-----------------------------------------------------------------------------
 
 
diff --git a/fpga-xc2s30/fpga_lf.v b/fpga-xc2s30/fpga_lf.v
index 3b37e44f0..bb9116f10 100644
--- a/fpga-xc2s30/fpga_lf.v
+++ b/fpga-xc2s30/fpga_lf.v
@@ -1,9 +1,21 @@
 //-----------------------------------------------------------------------------
-// Jonathan Westhues, March 2006
-// iZsh <izsh at fail0verflow.com>, June 2014
-// Piwi, Feb 2019
-// Anon, 2019
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
+//
+
+
 // Defining commands, modes and options. This must be aligned to the definitions in fpgaloader.h
 // Note: the definitions here are without shifts
 
diff --git a/fpga-xc2s30/hi_flite.v b/fpga-xc2s30/hi_flite.v
index c87a002cc..75ffbc9e5 100644
--- a/fpga-xc2s30/hi_flite.v
+++ b/fpga-xc2s30/hi_flite.v
@@ -1,3 +1,19 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+
 /*
   This code demodulates and modulates signal as described in ISO/IEC 18092.
   That includes packets used for Felica, NFC Tag 3, etc. (which do overlap)
diff --git a/fpga-xc2s30/hi_get_trace.v b/fpga-xc2s30/hi_get_trace.v
index 03a96af96..6e4369e14 100644
--- a/fpga-xc2s30/hi_get_trace.v
+++ b/fpga-xc2s30/hi_get_trace.v
@@ -1,6 +1,17 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
 //
-// piwi, Feb 2019
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
 
 module hi_get_trace(
diff --git a/fpga-xc2s30/hi_iso14443a.v b/fpga-xc2s30/hi_iso14443a.v
index 98a2d3930..81643b8c3 100644
--- a/fpga-xc2s30/hi_iso14443a.v
+++ b/fpga-xc2s30/hi_iso14443a.v
@@ -1,7 +1,20 @@
 //-----------------------------------------------------------------------------
-// ISO14443-A support for the Proxmark III
-// Gerhard de Koning Gans, April 2008
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
+// ISO14443-A support for the Proxmark III
+//
 
 module hi_iso14443a(
     ck_1356meg,
diff --git a/fpga-xc2s30/hi_reader.v b/fpga-xc2s30/hi_reader.v
index c5ffa7b85..dbc38a8a7 100644
--- a/fpga-xc2s30/hi_reader.v
+++ b/fpga-xc2s30/hi_reader.v
@@ -1,6 +1,17 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
 //
-// Jonathan Westhues, April 2006
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
 
 module hi_reader(
diff --git a/fpga-xc2s30/hi_reader_15.v b/fpga-xc2s30/hi_reader_15.v
index 357dc9e08..eaa141586 100644
--- a/fpga-xc2s30/hi_reader_15.v
+++ b/fpga-xc2s30/hi_reader_15.v
@@ -1,8 +1,20 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
 //
-// copied from hi_reader.v by Jonathan Westhues, April 2006
-// modified to add support for iso15 2sc mode by lnv42, Feb 2022
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
+// modified to add support for iso15 2sc mode
+//
 
 module hi_reader(
     ck_1356meg,
diff --git a/fpga-xc2s30/hi_simulate.v b/fpga-xc2s30/hi_simulate.v
index 33a6cf26c..2232aedd9 100644
--- a/fpga-xc2s30/hi_simulate.v
+++ b/fpga-xc2s30/hi_simulate.v
@@ -1,4 +1,19 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+//
 // Pretend to be an ISO 14443 tag. We will do this by alternately short-
 // circuiting and open-circuiting the antenna coil, with the tri-state
 // pins.
diff --git a/fpga-xc2s30/hi_sniffer.v b/fpga-xc2s30/hi_sniffer.v
index c2dc844a7..ed62191f1 100644
--- a/fpga-xc2s30/hi_sniffer.v
+++ b/fpga-xc2s30/hi_sniffer.v
@@ -1,3 +1,19 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+
 module hi_sniffer(
     ck_1356meg,
     pwr_lo, pwr_hi, pwr_oe1, pwr_oe2, pwr_oe3, pwr_oe4,
diff --git a/fpga-xc2s30/lf_edge_detect.v b/fpga-xc2s30/lf_edge_detect.v
index d086f95fc..4b6c0ac01 100644
--- a/fpga-xc2s30/lf_edge_detect.v
+++ b/fpga-xc2s30/lf_edge_detect.v
@@ -1,10 +1,21 @@
+
 //-----------------------------------------------------------------------------
-// Copyright (C) 2014 iZsh <izsh at fail0verflow.com>
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
 //
-// This code is licensed to you under the terms of the GNU GPL, version 2 or,
-// at your option, any later version. See the LICENSE.txt file for the text of
-// the license.
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
+
+
 // input clk is 24MHz
 `include "min_max_tracker.v"
 
diff --git a/fpga-xc2s30/lo_adc.v b/fpga-xc2s30/lo_adc.v
index bba090afb..808ec96ed 100644
--- a/fpga-xc2s30/lo_adc.v
+++ b/fpga-xc2s30/lo_adc.v
@@ -1,4 +1,19 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+//
 // The way that we connect things in low-frequency simulation mode. In this
 // case just pass everything through to the ARM, which can bit-bang this
 // (because it is so slow).
diff --git a/fpga-xc2s30/lo_edge_detect.v b/fpga-xc2s30/lo_edge_detect.v
index 5c73af96f..462dfcc46 100644
--- a/fpga-xc2s30/lo_edge_detect.v
+++ b/fpga-xc2s30/lo_edge_detect.v
@@ -1,9 +1,17 @@
 //-----------------------------------------------------------------------------
-// Copyright (C) 2014 iZsh <izsh at fail0verflow.com>
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
 //
-// This code is licensed to you under the terms of the GNU GPL, version 2 or,
-// at your option, any later version. See the LICENSE.txt file for the text of
-// the license.
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
 //
 // There are two modes:
@@ -16,6 +24,7 @@
 // Output:
 // - ssp_frame (wired to TIOA1 on the arm) for the edge detection/state
 // - ssp_clk: cross_lo
+
 `include "lp20khz_1MSa_iir_filter.v"
 `include "lf_edge_detect.v"
 
diff --git a/fpga-xc2s30/lo_passthru.v b/fpga-xc2s30/lo_passthru.v
index f0f2847a0..52e56d4c5 100644
--- a/fpga-xc2s30/lo_passthru.v
+++ b/fpga-xc2s30/lo_passthru.v
@@ -1,8 +1,22 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+//
 // For reading TI tags, we need to place the FPGA in pass through mode
 // and pass everything through to the ARM
-//-----------------------------------------------------------------------------
-// iZsh <izsh at fail0verflow.com>, June 2014
+
 
 module lo_passthru(
     input pck_divclk,
diff --git a/fpga-xc2s30/lo_read.v b/fpga-xc2s30/lo_read.v
index 8ac58721e..a7e481654 100644
--- a/fpga-xc2s30/lo_read.v
+++ b/fpga-xc2s30/lo_read.v
@@ -1,10 +1,24 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+//
 // The way that we connect things in low-frequency read mode. In this case
 // we are generating the unmodulated low frequency carrier.
 // The A/D samples at that same rate and the result is serialized.
 //
 // Jonathan Westhues, April 2006
-// iZsh <izsh at fail0verflow.com>, June 2014
 //-----------------------------------------------------------------------------
 
 module lo_read(
diff --git a/fpga-xc2s30/lo_simulate.v b/fpga-xc2s30/lo_simulate.v
index c4edc3eeb..89b548c73 100644
--- a/fpga-xc2s30/lo_simulate.v
+++ b/fpga-xc2s30/lo_simulate.v
@@ -1,4 +1,19 @@
 //-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+//
 // The way that we connect things in low-frequency simulation mode. In this
 // case just pass everything through to the ARM, which can bit-bang this
 // (because it is so slow).
diff --git a/fpga-xc2s30/lp20khz_1MSa_iir_filter.v b/fpga-xc2s30/lp20khz_1MSa_iir_filter.v
index 2dbfd6945..8bc6cca4f 100644
--- a/fpga-xc2s30/lp20khz_1MSa_iir_filter.v
+++ b/fpga-xc2s30/lp20khz_1MSa_iir_filter.v
@@ -1,10 +1,19 @@
 //-----------------------------------------------------------------------------
-// Copyright (C) 2014 iZsh <izsh at fail0verflow.com>
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
 //
-// This code is licensed to you under the terms of the GNU GPL, version 2 or,
-// at your option, any later version. See the LICENSE.txt file for the text of
-// the license.
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
+//
 // Butterworth low pass IIR filter
 // input: 8bit ADC signal, 1MS/s
 // output: 8bit value, Fc=20khz
@@ -26,6 +35,7 @@
 //
 // See details about its design see
 // https://fail0verflow.com/blog/2014/proxmark3-fpga-iir-filter.html
+
 module lp20khz_1MSa_iir_filter(input clk, input [7:0] adc_d, output rdy, output [7:0] out);
 
     // clk is 24MHz, the IIR filter is designed for 1MS/s
diff --git a/fpga-xc2s30/min_max_tracker.v b/fpga-xc2s30/min_max_tracker.v
index 5e8bbedf1..210d3c666 100644
--- a/fpga-xc2s30/min_max_tracker.v
+++ b/fpga-xc2s30/min_max_tracker.v
@@ -1,10 +1,19 @@
 //-----------------------------------------------------------------------------
-// Copyright (C) 2014 iZsh <izsh at fail0verflow.com>
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
 //
-// This code is licensed to you under the terms of the GNU GPL, version 2 or,
-// at your option, any later version. See the LICENSE.txt file for the text of
-// the license.
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
+//
 // track min and max peak values (envelope follower)
 //
 // NB: the min value (resp. max value) is updated only when the next high peak
@@ -13,6 +22,7 @@
 // This also means the peaks are detected with an unpredictable delay.
 // This algorithm therefore can't be used directly for realtime peak detections,
 // but it can be used as a simple envelope follower.
+
 module min_max_tracker(input clk, input [7:0] adc_d, input [7:0] threshold,
     output [7:0] min, output [7:0] max);
 
diff --git a/fpga-xc2s30/testbed_fpga.v b/fpga-xc2s30/testbed_fpga.v
index e33df789c..5acfeb536 100644
--- a/fpga-xc2s30/testbed_fpga.v
+++ b/fpga-xc2s30/testbed_fpga.v
@@ -1,3 +1,19 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+
 `include "fpga.v"
 
 module testbed_fpga;
diff --git a/fpga-xc2s30/testbed_hi_read_tx.v b/fpga-xc2s30/testbed_hi_read_tx.v
index ee28a0a61..e4f82bab7 100644
--- a/fpga-xc2s30/testbed_hi_read_tx.v
+++ b/fpga-xc2s30/testbed_hi_read_tx.v
@@ -1,3 +1,19 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+
 `include "hi_read_tx.v"
 
 /*
diff --git a/fpga-xc2s30/testbed_hi_simulate.v b/fpga-xc2s30/testbed_hi_simulate.v
index 2a2e2f799..02ea3370c 100644
--- a/fpga-xc2s30/testbed_hi_simulate.v
+++ b/fpga-xc2s30/testbed_hi_simulate.v
@@ -1,3 +1,19 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+
 `include "hi_simulate.v"
 
 /*
diff --git a/fpga-xc2s30/testbed_lo_read.v b/fpga-xc2s30/testbed_lo_read.v
index 0536a403f..dc0949e44 100644
--- a/fpga-xc2s30/testbed_lo_read.v
+++ b/fpga-xc2s30/testbed_lo_read.v
@@ -1,3 +1,19 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+
 `include "lo_read.v"
 /*
     pck0            - input main 24MHz clock (PLL / 4)
diff --git a/fpga-xc2s30/testbed_lo_simulate.v b/fpga-xc2s30/testbed_lo_simulate.v
index ec4bb73fe..5b06d3d57 100644
--- a/fpga-xc2s30/testbed_lo_simulate.v
+++ b/fpga-xc2s30/testbed_lo_simulate.v
@@ -1,3 +1,19 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+
 `include "lo_simulate.v"
 
 /*
diff --git a/fpga-xc2s30/util.v b/fpga-xc2s30/util.v
index 0842ac64f..66361ca59 100644
--- a/fpga-xc2s30/util.v
+++ b/fpga-xc2s30/util.v
@@ -1,8 +1,21 @@
 //-----------------------------------------------------------------------------
-// General-purpose miscellany.
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
 //
-// Jonathan Westhues, April 2006.
+// See LICENSE.txt for the text of the license.
 //-----------------------------------------------------------------------------
+//
+// General-purpose miscellany.
+//
 
 module mux8(sel, y, x0, x1, x2, x3, x4, x5, x6, x7);
     input [2:0] sel;
diff --git a/tools/fpga_compress/fpga_compress.c b/tools/fpga_compress/fpga_compress.c
index 66b0b7d4f..58b3a2162 100644
--- a/tools/fpga_compress/fpga_compress.c
+++ b/tools/fpga_compress/fpga_compress.c
@@ -336,14 +336,15 @@ static int FpgaGatherVersion(FILE *infile, char *infile_name, char *dst, int len
 
     if (!memcmp("fpga_lf", basename(infile_name), 7))
         strncat(dst, "LF", len - strlen(dst) - 1);
-    else if (!memcmp("fpga_hf", basename(infile_name), 7))
+    else if (!memcmp("fpga_hf_15", basename(infile_name), 10))
+        strncat(dst, "HF 15", len - strlen(dst) - 1);
+    else if (!memcmp("fpga_hf.", basename(infile_name), 8))
         strncat(dst, "HF", len - strlen(dst) - 1);
     else if (!memcmp("fpga_felica", basename(infile_name), 7))
         strncat(dst, "HF FeliCa", len - strlen(dst) - 1);
 
-    strncat(dst, " image built", len - strlen(dst) - 1);
+    strncat(dst, " image ", len - strlen(dst) - 1);
     if (bitparse_find_section(infile, 'b', &fpga_info_len)) {
-        strncat(dst, " for ", len - strlen(dst) - 1);
         for (uint32_t i = 0; i < fpga_info_len; i++) {
             char c = (char)fgetc(infile);
             if (i < sizeof(tempstr)) {
@@ -353,8 +354,8 @@ static int FpgaGatherVersion(FILE *infile, char *infile_name, char *dst, int len
         strncat(dst, tempstr, len - strlen(dst) - 1);
     }
 
+    strncat(dst, " ", len - strlen(dst) - 1);
     if (bitparse_find_section(infile, 'c', &fpga_info_len)) {
-        strncat(dst, " on ", len - strlen(dst) - 1);
         for (uint32_t i = 0; i < fpga_info_len; i++) {
             char c = (char)fgetc(infile);
             if (i < sizeof(tempstr)) {
@@ -367,7 +368,7 @@ static int FpgaGatherVersion(FILE *infile, char *infile_name, char *dst, int len
     }
 
     if (bitparse_find_section(infile, 'd', &fpga_info_len)) {
-        strncat(dst, " at ", len - strlen(dst) - 1);
+        strncat(dst, " ", len - strlen(dst) - 1);
         for (uint32_t i = 0; i < fpga_info_len; i++) {
             char c = (char)fgetc(infile);
             if (i < sizeof(tempstr)) {
@@ -382,9 +383,9 @@ static int FpgaGatherVersion(FILE *infile, char *infile_name, char *dst, int len
 
 static void print_version_info_preamble(FILE *outfile, int num_infiles) {
     fprintf(outfile, "//-----------------------------------------------------------------------------\n");
-    fprintf(outfile, "// piwi, 2018\n");
+    fprintf(outfile, "// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.\n");
     fprintf(outfile, "//\n");
-    fprintf(outfile, "// This code is licensed to you under the terms of the GNU GPL, version 2 or,\n");
+    fprintf(outfile, "// This code is licensed to you under the terms of the GNU GPL, version 3 or,\n");
     fprintf(outfile, "// at your option, any later version. See the LICENSE.txt file for the text of\n");
     fprintf(outfile, "// the license.\n");
     fprintf(outfile, "//-----------------------------------------------------------------------------\n");
@@ -392,7 +393,6 @@ static void print_version_info_preamble(FILE *outfile, int num_infiles) {
     fprintf(outfile, "//\n");
     fprintf(outfile, "// This file is generated by fpga_compress. Don't edit!\n");
     fprintf(outfile, "//-----------------------------------------------------------------------------\n");
-    fprintf(outfile, "// slurdge, 2020\n");
     fprintf(outfile, "\n\n");
     fprintf(outfile, "const int g_fpga_bitstream_num = %d;\n", num_infiles);
     fprintf(outfile, "const char *const g_fpga_version_information[%d] = {\n", num_infiles);
