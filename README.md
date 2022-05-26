commit d1671924527b9d606ee87b047a6a73af918991bd
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Mar 23 17:26:53 2022 +0100

    fix compilation problems with wrong xst,  cleaning up unused vars into mux and renamed module

diff --git a/fpga-xc2s30/Makefile b/fpga-xc2s30/Makefile
index d6f05c430..2bf9273ba 100644
--- a/fpga-xc2s30/Makefile
+++ b/fpga-xc2s30/Makefile
@@ -22,7 +22,7 @@ fpga_felica.ngc: fpga_felica.v fpga.ucf xst_felica.scr util.v hi_simulate.v hi_r
 	$(info [-] XST $@)
 	$(Q)$(XILINX_TOOLS_PREFIX)xst -ifn xst_felica.scr
 
-fpga_hf_15.ngc: fpga_hf_15.v fpga.ucf xst_hf.scr util.v hi_simulate.v hi_reader_15.v hi_iso14443a.v hi_sniffer.v hi_get_trace.v
+fpga_hf_15.ngc: fpga_hf_15.v fpga.ucf xst_hf_15.scr util.v hi_simulate.v hi_reader_15.v hi_sniffer.v hi_get_trace.v
 	$(Q)$(RM) $@
 	$(info [-] XST $@)
 	$(Q)$(XILINX_TOOLS_PREFIX)xst -ifn xst_hf_15.scr
diff --git a/fpga-xc2s30/fpga_hf_15.v b/fpga-xc2s30/fpga_hf_15.v
index b82ae7fc7..60f62e5d8 100644
--- a/fpga-xc2s30/fpga_hf_15.v
+++ b/fpga-xc2s30/fpga_hf_15.v
@@ -84,10 +84,8 @@
 
 `include "hi_reader_15.v"
 `include "hi_simulate.v"
-//`include "hi_iso14443a.v"
 `include "hi_sniffer.v"
 `include "util.v"
-// `include "hi_flite.v"
 `include "hi_get_trace.v"
 
 module fpga_hf_15(
@@ -171,8 +169,8 @@ wire [3:0] minor_mode = conf_word[3:0];
 // the output pins.
 //-----------------------------------------------------------------------------
 
-// 000 - HF reader
-hi_reader hr(
+// 000 - HF 15 reader
+hi_15_reader hr(
     ck_1356megb,
     hr_pwr_lo, hr_pwr_hi, hr_pwr_oe1, hr_pwr_oe2, hr_pwr_oe3, hr_pwr_oe4,
     adc_d, hr_adc_clk,
@@ -191,16 +189,6 @@ hi_simulate hs(
     minor_mode
 );
 
-/*// 010 - HF ISO14443-A
-hi_iso14443a hisn(
-    ck_1356meg,
-    hisn_pwr_lo, hisn_pwr_hi, hisn_pwr_oe1, hisn_pwr_oe2, hisn_pwr_oe3, hisn_pwr_oe4,
-    adc_d, hisn_adc_clk,
-    hisn_ssp_frame, hisn_ssp_din, ssp_dout, hisn_ssp_clk,
-    hisn_dbg,
-    minor_mode
-);*/
-
 // 011 - HF sniff
 hi_sniffer he(
     ck_1356megb,
@@ -209,18 +197,6 @@ hi_sniffer he(
     he_ssp_frame, he_ssp_din, he_ssp_clk
 );
 
-// 100 - HF ISO18092 FeliCa
-/*
-hi_flite hfl(
-    ck_1356megb,
-    hfl_pwr_lo, hfl_pwr_hi, hfl_pwr_oe1, hfl_pwr_oe2, hfl_pwr_oe3, hfl_pwr_oe4,
-    adc_d, hfl_adc_clk,
-    hfl_ssp_frame, hfl_ssp_din, ssp_dout, hfl_ssp_clk,
-    hfl_dbg,
-    minor_mode
-);
-*/
-
 // 101 - HF get trace
 hi_get_trace gt(
     ck_1356megb,
@@ -229,27 +205,27 @@ hi_get_trace gt(
 );
 
 // Major modes:
-//   000 --  HF reader; subcarrier frequency and modulation depth selectable
+//   000 --  HF 15 reader; subcarrier frequency and modulation depth selectable
 //   001 --  HF simulated tag
-//   010 --  HF ISO14443-A
+//   010 --  
 //   011 --  HF sniff
-//   100 --  HF ISO18092 FeliCa
+//   100 --  
 //   101 --  HF get trace
 //   110 --  unused
 //   111 --  FPGA_MAJOR_MODE_OFF
 
-//                                         000           001           010             011           100            101           110   111
-mux8 mux_ssp_clk   (major_mode, ssp_clk,   hr_ssp_clk,   hs_ssp_clk,   1'b0,   he_ssp_clk,   hfl_ssp_clk,   gt_ssp_clk,   1'b0, 1'b0);
-mux8 mux_ssp_din   (major_mode, ssp_din,   hr_ssp_din,   hs_ssp_din,   1'b0,   he_ssp_din,   hfl_ssp_din,   gt_ssp_din,   1'b0, 1'b0);
-mux8 mux_ssp_frame (major_mode, ssp_frame, hr_ssp_frame, hs_ssp_frame, 1'b0, he_ssp_frame, hfl_ssp_frame, gt_ssp_frame,   1'b0, 1'b0);
-mux8 mux_pwr_oe1   (major_mode, pwr_oe1,   hr_pwr_oe1,   hs_pwr_oe1,   1'b0,   he_pwr_oe1,   hfl_pwr_oe1,   1'b0,         1'b0, 1'b0);
-mux8 mux_pwr_oe2   (major_mode, pwr_oe2,   hr_pwr_oe2,   hs_pwr_oe2,   1'b0,   he_pwr_oe2,   hfl_pwr_oe2,   1'b0,         1'b0, 1'b0);
-mux8 mux_pwr_oe3   (major_mode, pwr_oe3,   hr_pwr_oe3,   hs_pwr_oe3,   1'b0,   he_pwr_oe3,   hfl_pwr_oe3,   1'b0,         1'b0, 1'b0);
-mux8 mux_pwr_oe4   (major_mode, pwr_oe4,   hr_pwr_oe4,   hs_pwr_oe4,   1'b0,   he_pwr_oe4,   hfl_pwr_oe4,   1'b0,         1'b0, 1'b0);
-mux8 mux_pwr_lo    (major_mode, pwr_lo,    hr_pwr_lo,    hs_pwr_lo,    1'b0,    he_pwr_lo,    hfl_pwr_lo,    1'b0,        1'b0, 1'b0);
-mux8 mux_pwr_hi    (major_mode, pwr_hi,    hr_pwr_hi,    hs_pwr_hi,    1'b0,    he_pwr_hi,    hfl_pwr_hi,    1'b0,        1'b0, 1'b0);
-mux8 mux_adc_clk   (major_mode, adc_clk,   hr_adc_clk,   hs_adc_clk,   1'b0,   he_adc_clk,   hfl_adc_clk,   1'b0,         1'b0, 1'b0);
-mux8 mux_dbg       (major_mode, dbg,       hr_dbg,       hs_dbg,       1'b0,       he_dbg,       hfl_dbg,       1'b0,         1'b0, 1'b0);
+//                                         000           001           010    011           100     101           110   111
+mux8 mux_ssp_clk   (major_mode, ssp_clk,   hr_ssp_clk,   hs_ssp_clk,   1'b0,  he_ssp_clk,   1'b0,   gt_ssp_clk,   1'b0, 1'b0);
+mux8 mux_ssp_din   (major_mode, ssp_din,   hr_ssp_din,   hs_ssp_din,   1'b0,  he_ssp_din,   1'b0,   gt_ssp_din,   1'b0, 1'b0);
+mux8 mux_ssp_frame (major_mode, ssp_frame, hr_ssp_frame, hs_ssp_frame, 1'b0,  he_ssp_frame, 1'b0,   gt_ssp_frame, 1'b0, 1'b0);
+mux8 mux_pwr_oe1   (major_mode, pwr_oe1,   hr_pwr_oe1,   hs_pwr_oe1,   1'b0,  he_pwr_oe1,   1'b0,   1'b0,         1'b0, 1'b0);
+mux8 mux_pwr_oe2   (major_mode, pwr_oe2,   hr_pwr_oe2,   hs_pwr_oe2,   1'b0,  he_pwr_oe2,   1'b0,   1'b0,         1'b0, 1'b0);
+mux8 mux_pwr_oe3   (major_mode, pwr_oe3,   hr_pwr_oe3,   hs_pwr_oe3,   1'b0,  he_pwr_oe3,   1'b0,   1'b0,         1'b0, 1'b0);
+mux8 mux_pwr_oe4   (major_mode, pwr_oe4,   hr_pwr_oe4,   hs_pwr_oe4,   1'b0,  he_pwr_oe4,   1'b0,   1'b0,         1'b0, 1'b0);
+mux8 mux_pwr_lo    (major_mode, pwr_lo,    hr_pwr_lo,    hs_pwr_lo,    1'b0,  he_pwr_lo,    1'b0,   1'b0,         1'b0, 1'b0);
+mux8 mux_pwr_hi    (major_mode, pwr_hi,    hr_pwr_hi,    hs_pwr_hi,    1'b0,  he_pwr_hi,    1'b0,   1'b0,         1'b0, 1'b0);
+mux8 mux_adc_clk   (major_mode, adc_clk,   hr_adc_clk,   hs_adc_clk,   1'b0,  he_adc_clk,   1'b0,   1'b0,         1'b0, 1'b0);
+mux8 mux_dbg       (major_mode, dbg,       hr_dbg,       hs_dbg,       1'b0,  he_dbg,       1'b0,   1'b0,         1'b0, 1'b0);
 
 // In all modes, let the ADC's outputs be enabled.
 assign adc_noe = 1'b0;
diff --git a/fpga-xc2s30/hi_reader_15.v b/fpga-xc2s30/hi_reader_15.v
index eaa141586..b60eafa0c 100644
--- a/fpga-xc2s30/hi_reader_15.v
+++ b/fpga-xc2s30/hi_reader_15.v
@@ -16,7 +16,7 @@
 // modified to add support for iso15 2sc mode
 //
 
-module hi_reader(
+module hi_15_reader(
     ck_1356meg,
     pwr_lo, pwr_hi, pwr_oe1, pwr_oe2, pwr_oe3, pwr_oe4,
     adc_d, adc_clk,
