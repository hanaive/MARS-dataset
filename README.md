# MARS: Multispectral All-weather River & Sea dataset

A multispectral (visible–thermal) object detection benchmark for **unmanned surface
vehicles (USVs)** and waterborne traffic, collected from a surface-level viewpoint under
the low-visibility conditions that matter most for safe navigation: dense fog, low light,
and intense water glare.

> **Status:** the images, annotations, and evaluation scripts are being prepared and will
> be released in this repository **upon acceptance of the accompanying paper**. The
> structure below documents what will be published. Watch this repository for the release.

## Why this dataset

Existing multispectral benchmarks are collected for road or aerial scenes (KAIST, LLVIP,
RGBT-Tiny) and do not match the viewpoint, target scale, or scene statistics of a surface
vessel. Existing maritime benchmarks cover waterborne scenes but rely on single-modality,
camera–radar, or LiDAR–camera sensing and provide no aligned visible–thermal pairs. MARS
fills that gap.

## At a glance

| | |
| --- | --- |
| Platform / viewpoint | USV, surface level (eye-level to slightly downward) |
| Modalities | Visible (VIS) + long-wave thermal infrared (IR) |
| Image pairs | 2,549 |
| Annotated instances | 11,617 |
| Categories | 7 |
| Image resolution | 1280 × 1024 |
| Alignment | Geometrically aligned; mean keypoint offset 2.06 px (std 0.91 px) |
| Conditions covered | Dense fog, low light, intense water glare, plus normal daylight, overcast, and nighttime scenes |
| Splits | 7 : 1 : 2 → 1,784 train / 255 val / 510 test pairs |

**Categories:** cargo ship, small craft, passenger ship, container ship, sailboat, buoy,
and unclassified ship.

**Scale distribution** (COCO convention, computed on the 1280 × 1024 images):
small (< 32² px) 47.27 %, medium (32²–96² px) 38.62 %, large (> 96² px) 14.12 %.

## Acquisition and alignment

A horizontal short-baseline binocular synchronous vision system:

* VIS camera — 2592 × 1944, wide field of view
* Thermal IR camera — 8–14 µm, 640 × 512
* Baseline — 3.5 cm

Alignment proceeds in three steps: hardware stereo calibration to remove global parallax,
a local homography projection of the thermal image into the visible image frame, and
resolution normalization to a common 1280 × 1024. Because the projection is a single
homography it is exact only on the assumed ground plane, so the residual grows with an
object's distance from that plane.

## Planned repository layout

```
MARS/
├── images/            visible images          (1280 × 1024 JPG)
├── images_thermal/    thermal images          (1280 × 1024 JPG)
├── labels/            YOLO-format annotations (class cx cy w h, normalised)
├── splits/            train.txt / val.txt / test.txt
├── subsets/           dense_fog.txt / low_light.txt / glare.txt / normal.txt
└── classes.txt        the seven category names
```

Annotations use the YOLO text format so that the dataset can be used without a converter:
one line per instance, `class_id cx cy w h`, with the centre and size normalised to
`[0, 1]`. The two channels of a pair share a single set of boxes: a target invisible in
the visible channel but clearly present in the thermal channel is annotated at its
thermal position, and vice versa.

## Evaluation protocol

Report mAP@0.5 and mAP@0.5:0.95 on the test split. For the adverse-condition analysis,
evaluate each method on the four subsets listed in `subsets/` separately. Because each
subset contains only tens of image pairs, report the **gain over the visible-only
detector** together with a paired bootstrap confidence interval over images rather than
absolute mAP alone.

## License

To be determined at release. Please open an issue if you need the terms before then.

## Citation

The accompanying paper is under review. A BibTeX entry will be added here on publication.

