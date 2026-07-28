# CDet detector model

## Physical hierarchy and arithmetic

| Level | Count | Meaning |
|---|---:|---|
| Layers | 2 | Detector layers |
| Modules | 6 | `2 layers × 3 modules/layer` |
| Sides | 12 | A mirror divides each module into left and right sides |
| Bars / maPMTs | 168 | `12 sides × 14 bars/side` |
| Total maPMT pixels | 2688 | `168 bars × 16 pixels/bar` |
| Unused pixels | 336 | `168 bars × 2 unused pixels/bar` |
| Instrumented paddles | 2352 | `168 bars × 14 paddles/bar` |

One bar is associated with one 16-pixel maPMT. Fourteen pixels carry signals from the bar's 14 scintillator paddles; two pixels are unused.

## Ordering and coordinates

Physical paddles/channels are ordered bottom-to-top and then left-to-right. In transport coordinates, the physically bottom channel corresponds to positive `x`. A physical position, detector bar, maPMT pixel, electronics channel, and database logical-channel ID are different concepts and must not be used interchangeably.

No verified formula mapping module/side/bar/paddle positions to database logical IDs is documented yet. Such a formula must be derived from authoritative mapping information, not inferred from consecutive numbers.

## Database observations

The following were verified directly on 2026-07-27 in `git-repo/sbs_devel/SBS-replay/DB/db_earm.cdet.dat`:

- `#earm.cdet.nchan = 2720` is commented out; it is not evidence that 2720 is the active mapped-channel count.
- The detector map lists channel ranges totaling 2704 entries: 2688 detector pixel channels followed by four four-channel ranges in DAQ slot 20 (16 additional channels).
- The final channel-map line contains logical IDs 2688 through 2703.
- `earm.cdet.nrows = 4` and `earm.cdet.ncols = 676`, so the declared grid contains `4 × 676 = 2704` logical elements.
- `earm.cdet.nlayers = 1`; the database's four rows encode the two physical layers and two sides for purposes of `SBSGenericDetector`. This software grid is not the same hierarchy as the physical two-layer detector.
- Logical ID 2696 corresponds by explicit map order to crate 63, slot 20, channel 40.

The project leader confirms that 2704 is the correct mapped total. IDs 0–2687 are the physical detector pixels. The extra DAQ slot contributes 16 mapped IDs, but only ID 2696 is used, as the reference signal; the other 15 are not physical CDet channels. The commented 2720 value is therefore not an active channel count.

## Implemented software interpretation

`SBSGenericDetector::ReadDatabase` computes its element count as the sum of `ncols[row] × nlayers`, then requires the detector-map and channel-map counts to match. With this database it therefore constructs 2704 elements. Its special reference-channel convention is a channel-map value of `-1000`; the current CDet channel map contains ordinary logical IDs 2688–2703 instead, and the detector-map reference fields are `-1`. Consequently, the generic detector represents the extra slot as ordinary elements even though the CDet analysis uses ID 2696 as its reference.

`SBSCDet` operates in TDC mode with leading and trailing edges and no ADC by default. It delegates decoding and good-hit choice to `SBSGenericDetector`, then retains hits inside hard-coded leading-edge and TOT windows of 100–10000 in the detector's processed units. It copies logical element ID, software row/column/layer, database position, leading edge, trailing edge, and TOT into `earm.cdet.hit.*`. `FineProcess` currently performs no clustering or track matching.

The current cross-target calibration macro treats IDs 0–2687 as 168 groups of 16 and commonly computes a bar index as `logical ID / 16`. It calls all 16 entries “paddles,” although physically only 14 pixels are connected to paddles. Treat this as an implemented analysis convention, not a verified physical-to-logical formula. The macro also contains internally inconsistent count declarations: `2688 + 4` is commented as 2704, while the database contains 16 extra IDs. Code review is required before relying on those constants for bounds or histogram sizes.

## Unresolved points

- Why the historical commented value 2720 remains in the database is unknown, but it is not an active mapping question: 2704 is the confirmed mapped total.
- The software representation of the mostly unused extra slot may eventually be cleaned up, but its present purpose is known: ID 2696 is the reference and the other 15 IDs are not physical detector channels.
- What is the verified mapping among physical paddles, bars, maPMT pixels, electronics channels, and database logical IDs?
- Where in the upstream offline processing is the established ROOT-output convention of 10 ps per stored CDet timing unit applied?

## Timing-unit convention

The project leader confirms that CDet timing quantities in replayed ROOT files are stored in units of 10 ps. The `PlotElastic*.C` calibration macros multiply these values by `0.01` to express them in ns:

`t [ns] = 0.01 × t [10 ps units]`.

The exact upstream conversion point has not yet been located and may be in general SBS processing rather than a CDet-specific class. The present convention is accepted for ongoing analysis. A future change must be treated as a coordinated calibration/interface change so that replay output, cuts, calibration constants, plots, and historical ROOT files are not silently mixed.
