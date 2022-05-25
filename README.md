commit 72f26825448eaa5a3935efeb2d7f4435642cc184
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 21:11:51 2022 +0100

    cppcheck

diff --git a/client/src/cmdhfwaveshare.c b/client/src/cmdhfwaveshare.c
index 01dc7d63c..dd62633c2 100644
--- a/client/src/cmdhfwaveshare.c
+++ b/client/src/cmdhfwaveshare.c
@@ -159,7 +159,8 @@ static int read_bmp_bitmap(const uint8_t *bmp, const size_t bmpsize, uint8_t mod
     return PM3_SUCCESS;
 }
 
-static void rgb_to_gray(int16_t *chanR, int16_t *chanG, int16_t *chanB, uint16_t width, uint16_t height, int16_t *chanGrey) {
+static void rgb_to_gray(const int16_t *chanR, const int16_t *chanG, const int16_t *chanB,
+                        uint16_t width, uint16_t height, int16_t *chanGrey) {
     for (uint16_t Y = 0; Y < height; Y++) {
         for (uint16_t X = 0; X < width; X++) {
             // greyscale conversion
@@ -204,7 +205,8 @@ static uint32_t color_compare(int16_t r1, int16_t g1, int16_t b1, int16_t r2, in
     return dist;
 }
 
-static void nearest_color(int16_t oldR, int16_t oldG, int16_t oldB, uint8_t *palette, uint16_t palettelen, uint8_t *newR, uint8_t *newG, uint8_t *newB) {
+static void nearest_color(int16_t oldR, int16_t oldG, int16_t oldB, const uint8_t *palette,
+                          uint16_t palettelen, uint8_t *newR, uint8_t *newG, uint8_t *newB) {
     uint32_t bestdist = 0x7FFFFFFF;
     for (uint16_t i = 0; i < palettelen; i++) {
         uint8_t R = palette[i * 3 + 0];
@@ -296,7 +298,7 @@ static void rgb_to_gray_red_inplace(int16_t *chanR, int16_t *chanG, int16_t *cha
     }
 }
 
-static void threshold_chan(int16_t *colorchan, uint16_t width, uint16_t height, uint8_t threshold, uint8_t *colormap) {
+static void threshold_chan(const int16_t *colorchan, uint16_t width, uint16_t height, uint8_t threshold, uint8_t *colormap) {
     for (uint16_t Y = 0; Y < height; Y++) {
         for (uint16_t X = 0; X < width; X++) {
             colormap[X + Y * width] = colorchan[X + Y * width] < threshold;
@@ -304,7 +306,9 @@ static void threshold_chan(int16_t *colorchan, uint16_t width, uint16_t height,
     }
 }
 
-static void threshold_rgb_black_red(int16_t *chanR, int16_t *chanG, int16_t *chanB, uint16_t width, uint16_t height, uint8_t threshold_black, uint8_t threshold_red, uint8_t *blackmap, uint8_t *redmap) {
+static void threshold_rgb_black_red(const int16_t *chanR, const int16_t *chanG, const int16_t *chanB,
+                                    uint16_t width, uint16_t height, uint8_t threshold_black,
+                                    uint8_t threshold_red, uint8_t *blackmap, uint8_t *redmap) {
     for (uint16_t Y = 0; Y < height; Y++) {
         for (uint16_t X = 0; X < width; X++) {
             if ((chanR[X + Y * width] < threshold_black) && (chanG[X + Y * width] < threshold_black) && (chanB[X + Y * width] < threshold_black)) {
@@ -321,7 +325,7 @@ static void threshold_rgb_black_red(int16_t *chanR, int16_t *chanG, int16_t *cha
     }
 }
 
-static void map8to1(uint8_t *colormap, uint16_t width, uint16_t height, uint8_t *colormap8) {
+static void map8to1(const uint8_t *colormap, uint16_t width, uint16_t height, uint8_t *colormap8) {
     uint16_t width8;
     if (width % 8 == 0) {
         width8 = width / 8;
@@ -532,12 +536,12 @@ static int read_bmp_rgb(uint8_t *bmp, const size_t bmpsize, uint8_t model_nr, ui
     return PM3_SUCCESS;
 }
 
-static void read_black(uint32_t i, uint8_t *l, uint8_t model_nr, uint8_t *black) {
+static void read_black(uint32_t i, uint8_t *l, uint8_t model_nr, const uint8_t *black) {
     for (uint8_t j = 0; j < models[model_nr].len; j++) {
         l[3 + j] = black[i * models[model_nr].len + j];
     }
 }
-static void read_red(uint32_t i, uint8_t *l, uint8_t model_nr, uint8_t *red) {
+static void read_red(uint32_t i, uint8_t *l, uint8_t model_nr, const uint8_t *red) {
     // spurious warning with GCC10 (-Wstringop-overflow) when j is uint8_t, even if all len are < 128
     for (uint16_t j = 0; j < models[model_nr].len; j++) {
         if (model_nr == M1in54B) {
