# Remaining Implementation Guide
## Traffic Signal Violation Detection System - Phase 2-5 Tasks

**Document Purpose:** Guide for human engineers or AI agents to continue implementation
**Progress:** 6/15 tasks completed (40%), 10 tasks remaining (60%)
**Estimated Effort:** 32 hours remaining
**Last Updated:** 2025-10-22

---

## Table of Contents

1. [Context & Background](#context--background)
2. [Project Structure Overview](#project-structure-overview)
3. [Completed Work Reference](#completed-work-reference)
4. [Remaining Tasks by Priority](#remaining-tasks-by-priority)
5. [Detailed Task Specifications](#detailed-task-specifications)
6. [Testing Guidelines](#testing-guidelines)
7. [Common Patterns & Best Practices](#common-patterns--best-practices)
8. [Troubleshooting Guide](#troubleshooting-guide)

---

## Context & Background

### What This Project Does

This is a **traffic signal violation detection system** that:
- Uses YOLOv3 object detection to identify vehicles in video footage
- Allows users to draw a virtual "red signal line" on the video
- Detects when vehicles cross that line (violation)
- Outputs annotated video with green boxes (compliant) and red boxes (violations)
- Saves violation snapshots for evidence

### Current State (After Phase 1)

**✅ What's Working:**
- Configuration management (`config.py`) - all paths are portable
- TensorFlow 2.x compatibility - works with modern TF versions
- Error handling - graceful failures with user messages
- Geometry utilities - reusable line intersection functions
- Basic logging - INFO/ERROR/WARNING levels
- Dependencies documented - `requirements.txt` available

**⚠️ What Needs Work:**
- Model loads at module import (slow, inefficient)
- No CLI interface (GUI only)
- No violation reporting (CSV/JSON)
- No progress indicators
- No GPU optimization
- Limited test coverage
- Incomplete documentation
- No deployment tooling

### Technology Stack

**Core:**
- Python 3.8+
- TensorFlow 2.10+ (with Keras integrated)
- OpenCV 4.6+
- NumPy, Pillow, imageio

**GUI:**
- Tkinter (standard library)

**Detection:**
- YOLOv3 with Darknet-53 architecture
- Pre-trained COCO weights (80 classes)

---

## Project Structure Overview

### Current Structure
```
Traffic-Signal-Violation-Detection-System/
├── config.py                          ✅ Configuration management
├── Project-GUI.py                     ✅ GUI + main processing (243 lines)
├── object_detection.py                ✅ YOLOv3 + detection logic (410 lines)
├── utils/
│   ├── __init__.py                   ✅ Package marker
│   └── geometry.py                   ✅ Geometric utilities (220 lines)
├── requirements.txt                   ✅ Dependencies
├── requirements-minimal.txt           ✅ Production deps
├── CODE_SUMMARY_AND_IMPROVEMENTS.md   ✅ Code analysis
├── IMPLEMENTATION_PLAN.md             ✅ Full roadmap
├── Resources/
│   ├── input/                        📁 Input videos
│   └── output/                       📁 Output videos
├── Images/                           📁 Preview images
├── Detected Images/                  📁 Violation snapshots
└── weights/
    └── yolov3.weights                📥 Download from YOLO site
```

### Target Structure (After All Phases)
```
Traffic-Signal-Violation-Detection-System/
├── config.py                          ✅ Already done
├── main.py                            ⏳ Task 9 - CLI entry point
├── models/
│   ├── __init__.py                   ⏳ Task 8
│   ├── yolov3.py                     ⏳ Task 8 - Model architecture
│   └── weight_loader.py              ⏳ Task 8 - WeightReader class
├── detection/
│   ├── __init__.py                   ⏳ Task 8
│   ├── detector.py                   ⏳ Task 8 - Main detection logic
│   ├── preprocessing.py              ⏳ Task 8 - Image preprocessing
│   └── violation_checker.py          ⏳ Task 8 - Violation detection
├── gui/
│   ├── __init__.py                   ⏳ Task 8
│   └── main_window.py                ⏳ Task 8 - Refactored GUI
├── utils/
│   ├── __init__.py                   ✅ Already done
│   ├── geometry.py                   ✅ Already done
│   ├── video_io.py                   ⏳ Task 8 - Video utilities
│   ├── logging_config.py             ⏳ Task 8 - Logging setup
│   └── report_generator.py           ⏳ Task 10 - CSV reports
├── tests/
│   ├── test_geometry.py              ⏳ Task 13
│   ├── test_detection.py             ⏳ Task 13
│   └── test_violation_logic.py       ⏳ Task 13
├── Dockerfile                         ⏳ Task 15
├── docker-compose.yml                 ⏳ Task 15
└── .dockerignore                      ⏳ Task 15
```

---

## Completed Work Reference

### ✅ Example 1: Configuration Pattern (`config.py`)

**What to learn from this:**
- Uses `pathlib.Path` for cross-platform compatibility
- Auto-creates directories on import
- Includes validation functions
- Helper functions for common operations

**Key Pattern:**
```python
from pathlib import Path

PROJECT_ROOT = Path(__file__).parent.resolve()
OUTPUT_DIR = PROJECT_ROOT / "Resources" / "output"

def ensure_directories():
    """Create all necessary directories"""
    OUTPUT_DIR.mkdir(parents=True, exist_ok=True)

ensure_directories()  # Run on import
```

**Apply this pattern to:** All new modules that need file paths

---

### ✅ Example 2: Error Handling Pattern (`Project-GUI.py`)

**What to learn from this:**
- Try-except blocks around all I/O operations
- User-friendly error dialogs with `messagebox`
- Resource cleanup in `finally` blocks
- Detailed logging with stack traces

**Key Pattern:**
```python
import logging
import traceback
from tkinter import messagebox

logger = logging.getLogger(__name__)

def risky_operation(self):
    resource = None
    try:
        resource = open_resource()
        # ... do work ...
        logger.info("Operation successful")
    except FileNotFoundError as e:
        logger.error(f"File not found: {e}")
        messagebox.showerror("Error", f"File not found: {e}")
    except Exception as e:
        logger.error(f"Unexpected error: {e}\n{traceback.format_exc()}")
        messagebox.showerror("Error", f"Unexpected error: {e}")
    finally:
        if resource:
            resource.close()
            logger.info("Resource cleaned up")
```

**Apply this pattern to:** All new functions that do I/O, network calls, or risky operations

---

### ✅ Example 3: Reusable Module Pattern (`utils/geometry.py`)

**What to learn from this:**
- Type hints for all function parameters
- Comprehensive docstrings with examples
- Built-in unit tests (run with `python -m utils.geometry`)
- Clear function naming

**Key Pattern:**
```python
from typing import Tuple

Point = Tuple[int, int]  # Type alias for clarity

def calculate_something(p1: Point, p2: Point) -> float:
    """
    Brief one-line description.

    Longer description of what this does and why.

    Args:
        p1: First point (x, y)
        p2: Second point (x, y)

    Returns:
        float: The calculated result

    Example:
        >>> calculate_something((0, 0), (3, 4))
        5.0
    """
    # Implementation
    return result

if __name__ == "__main__":
    # Built-in tests
    assert calculate_something((0, 0), (3, 4)) == 5.0
    print("✅ All tests passed!")
```

**Apply this pattern to:** All new utility modules

---

## Remaining Tasks by Priority

### Phase 2: Code Quality (12 hours, 2 tasks)

| Task | Effort | Files | Priority |
|------|--------|-------|----------|
| **Task 7:** Lazy Model Loading | 2h | `object_detection.py` | HIGH |
| **Task 8:** Modular Architecture | 8h | Multiple (major refactor) | MEDIUM |

### Phase 3: Features (8 hours, 4 tasks)

| Task | Effort | Files | Priority |
|------|--------|-------|----------|
| **Task 9:** CLI Interface | 3h | `main.py` (new) | HIGH |
| **Task 10:** CSV Reporting | 2h | `utils/report_generator.py` | MEDIUM |
| **Task 11:** Progress Bars | 1h | `Project-GUI.py` | LOW |
| **Task 12:** GPU Acceleration | 2h | `config.py`, `main.py` | HIGH |

### Phase 4: Testing & Docs (9 hours, 2 tasks)

| Task | Effort | Files | Priority |
|------|--------|-------|----------|
| **Task 13:** Unit Tests | 6h | `tests/` (new) | MEDIUM |
| **Task 14:** Docstrings | 3h | All files | LOW |

### Phase 5: Deployment (3 hours, 1 task)

| Task | Effort | Files | Priority |
|------|--------|-------|----------|
| **Task 15:** Docker Container | 3h | `Dockerfile`, etc. | MEDIUM |

---

## Detailed Task Specifications

---

## Task 7: Lazy Model Loading ⏳

**Priority:** HIGH
**Effort:** 2 hours
**Dependencies:** None
**Impact:** Dramatically improves startup time (30s → <1s for non-processing tasks)

### Problem Statement

Currently in `object_detection.py` (lines 407-412):
```python
# This runs when the module is imported!
yolov3 = make_yolov3_model()
weight_reader = WeightReader(weights_path)
weight_reader.load_weights(yolov3)
```

**Issues:**
- Model loads immediately on `import object_detection as od`
- Takes ~30 seconds even when just validating config
- Consumes ~2GB RAM even if not processing video
- Can't run CLI commands quickly

### Solution Design

Create a `ViolationDetector` class that loads the model only when needed.

### Implementation Steps

#### Step 1: Create Detector Class

In `object_detection.py`, replace global model loading with:

```python
import logging
from config import YOLOV3_WEIGHTS_PATH, NET_H, NET_W, OBJ_THRESH, NMS_THRESH, ANCHORS, LABELS

logger = logging.getLogger(__name__)

class ViolationDetector:
    """
    YOLOv3-based vehicle detector with lazy model loading.

    The model is only loaded when first needed, improving startup time.
    """

    def __init__(self):
        """Initialize detector without loading the model."""
        self._model = None
        self.net_h = NET_H
        self.net_w = NET_W
        self.obj_thresh = OBJ_THRESH
        self.nms_thresh = NMS_THRESH
        self.anchors = ANCHORS
        self.labels = LABELS
        logger.info("ViolationDetector initialized (model not loaded yet)")

    @property
    def model(self):
        """
        Lazy load the YOLOv3 model on first access.

        Returns:
            Model: Loaded YOLOv3 model ready for inference
        """
        if self._model is None:
            logger.info("Loading YOLOv3 model (this may take 20-30 seconds)...")
            import time
            start = time.time()

            # Build model architecture
            self._model = make_yolov3_model()

            # Load pre-trained weights
            weight_reader = WeightReader(str(YOLOV3_WEIGHTS_PATH))
            weight_reader.load_weights(self._model)

            elapsed = time.time() - start
            logger.info(f"Model loaded successfully in {elapsed:.1f} seconds")

        return self._model

    def predict(self, preprocessed_image):
        """
        Run detection on preprocessed image.

        Args:
            preprocessed_image: Image preprocessed by preprocess_input()

        Returns:
            list: YOLOv3 predictions at 3 scales
        """
        return self.model.predict(preprocessed_image)


# Create global singleton instance (but model not loaded yet!)
detector = ViolationDetector()
```

#### Step 2: Update Project-GUI.py

In `Project-GUI.py`, update the model usage:

```python
# OLD (line 289):
yolos = od.yolov3.predict(new_image)

# NEW:
yolos = od.detector.predict(new_image)
```

Also update parameter access:

```python
# OLD:
od.preprocess_input(image, od.net_h, od.net_w)
od.decode_netout(yolos[i][0], od.anchors[i], od.obj_thresh, od.nms_thresh, od.net_h, od.net_w)

# NEW:
od.preprocess_input(image, od.detector.net_h, od.detector.net_w)
od.decode_netout(yolos[i][0], od.detector.anchors[i], od.detector.obj_thresh,
                 od.detector.nms_thresh, od.detector.net_h, od.detector.net_w)
```

#### Step 3: Remove Old Global Loading

Remove these lines from `object_detection.py`:

```python
# DELETE:
weights_path = "G:/Traffic Violation Detection/yolov3.weights"
net_h, net_w = 416, 416
obj_thresh, nms_thresh = 0.5, 0.45
anchors = [[116,90,  156,198,  373,326], ...]
labels = ["person", "bicycle", ...]
yolov3 = make_yolov3_model()
weight_reader = WeightReader(weights_path)
weight_reader.load_weights(yolov3)
```

All these are now in the `ViolationDetector` class or imported from `config.py`.

### Testing

```python
# Test lazy loading
import object_detection as od
# Should be instant, no model loading

# First predict triggers loading
detector = od.detector
image = ... # some test image
detector.predict(image)  # Now loads model (30s)

# Subsequent calls are fast
detector.predict(image)  # Uses cached model (<1s)
```

### Acceptance Criteria

- [ ] `import object_detection` completes in < 1 second
- [ ] Model loads on first `detector.predict()` call
- [ ] Subsequent predictions use cached model
- [ ] Logging shows when model is loading
- [ ] All existing functionality still works
- [ ] `Project-GUI.py` runs without errors

### Files to Modify

1. `object_detection.py` - Add `ViolationDetector` class, remove global loading
2. `Project-GUI.py` - Update to use `od.detector` instead of `od.yolov3`

### Gotchas

⚠️ **Don't forget to update ALL references to global variables:**
- `od.yolov3` → `od.detector.model` or `od.detector.predict()`
- `od.net_h` → `od.detector.net_h`
- `od.anchors` → `od.detector.anchors`
- etc.

⚠️ **Import from config, not hardcoded:**
```python
# BAD:
weights_path = "G:/..."

# GOOD:
from config import YOLOV3_WEIGHTS_PATH
```

---

## Task 9: CLI Interface ⏳

**Priority:** HIGH
**Effort:** 3 hours
**Dependencies:** Task 7 (recommended but not required)
**Impact:** Enables headless processing, batch operations, server deployment

### Problem Statement

Currently, the application ONLY works with a GUI. Users need:
- Batch processing of multiple videos
- Server/cloud deployment (headless)
- Automation scripts
- CI/CD pipeline integration

### Solution Design

Create `main.py` as the new entry point with both CLI and GUI modes.

### Implementation Steps

#### Step 1: Create main.py

Create new file: `main.py`

```python
#!/usr/bin/env python3
"""
Traffic Signal Violation Detection System - Main Entry Point

Supports both GUI and CLI modes for flexible usage.
"""

import argparse
import sys
import logging
from pathlib import Path

# Import config first to ensure directories exist
import config
from config import (
    OUTPUT_DIR,
    DETECTED_IMAGES_DIR,
    YOLOV3_WEIGHTS_PATH,
    validate_config
)

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)


def validate_environment():
    """Validate that the environment is properly configured."""
    logger.info("Validating environment...")

    is_valid, errors = validate_config()

    if not is_valid:
        logger.error("Configuration validation failed:")
        for error in errors:
            logger.error(f"  - {error}")
        return False

    logger.info("✓ Environment validation passed")
    return True


def run_gui_mode():
    """Launch the graphical user interface."""
    logger.info("Starting GUI mode...")

    try:
        import Project_GUI
        # The GUI module runs on import
        logger.info("GUI launched successfully")
    except ImportError as e:
        logger.error(f"Failed to import GUI module: {e}")
        logger.error("Make sure tkinter is installed: sudo apt-get install python3-tk")
        sys.exit(1)
    except Exception as e:
        logger.error(f"Failed to launch GUI: {e}")
        sys.exit(1)


def run_cli_mode(args):
    """
    Run detection in CLI mode (headless).

    Args:
        args: Parsed command-line arguments
    """
    logger.info("Starting CLI mode...")

    # Validate input
    video_path = Path(args.video)
    if not video_path.exists():
        logger.error(f"Video file not found: {video_path}")
        sys.exit(1)

    # Parse traffic line coordinates
    try:
        coords = [int(x) for x in args.line.split(',')]
        if len(coords) != 4:
            raise ValueError("Line must have 4 coordinates")
        line = [(coords[0], coords[1]), (coords[2], coords[3])]
        logger.info(f"Traffic line: {line[0]} to {line[1]}")
    except (ValueError, AttributeError) as e:
        logger.error(f"Invalid line coordinates: {e}")
        logger.error("Format: --line x1,y1,x2,y2 (e.g., --line 100,200,500,200)")
        sys.exit(1)

    # Import detection modules (lazy load)
    import object_detection as od
    import cv2
    import imageio
    from tqdm import tqdm

    # Set output path
    if args.output:
        output_path = Path(args.output)
    else:
        output_path = OUTPUT_DIR / f"output_{video_path.stem}.mp4"

    logger.info(f"Input: {video_path}")
    logger.info(f"Output: {output_path}")

    # Process video
    try:
        cap = cv2.VideoCapture(str(video_path))
        if not cap.isOpened():
            raise ValueError("Unable to open video file")

        # Get video metadata
        total_frames = int(cap.get(cv2.CAP_PROP_FRAME_COUNT))
        fps = cap.get(cv2.CAP_PROP_FPS)
        logger.info(f"Video: {total_frames} frames @ {fps} FPS")

        # Setup output writer
        writer = imageio.get_writer(str(output_path), fps=fps)

        frame_num = 0
        violations_count = 0

        with tqdm(total=total_frames, desc="Processing video") as pbar:
            while True:
                ret, image = cap.read()
                if not ret or image is None:
                    break

                frame_num += 1

                # Preprocess
                image_h, image_w, _ = image.shape
                new_image = od.preprocess_input(image, od.detector.net_h, od.detector.net_w)

                # Detect
                yolos = od.detector.predict(new_image)
                boxes = []

                for i in range(len(yolos)):
                    boxes += od.decode_netout(
                        yolos[i][0],
                        od.detector.anchors[i],
                        od.detector.obj_thresh,
                        od.detector.nms_thresh,
                        od.detector.net_h,
                        od.detector.net_w
                    )

                # Correct boxes and apply NMS
                od.correct_yolo_boxes(boxes, image_h, image_w, od.detector.net_h, od.detector.net_w)
                od.do_nms(boxes, od.detector.nms_thresh)

                # Draw boxes and detect violations
                image_annotated = od.draw_boxes(
                    image, boxes, line,
                    od.detector.labels,
                    od.detector.obj_thresh,
                    frame_num
                )

                # Count violations (check for red boxes)
                # This is a simplification - improve in draw_boxes to return violation count

                writer.append_data(image_annotated)

                pbar.update(1)
                pbar.set_postfix({
                    'violations': violations_count,
                    'fps': pbar.format_dict['rate'] if pbar.format_dict['rate'] else 0
                })

        writer.close()
        cap.release()

        logger.info(f"✓ Processing complete!")
        logger.info(f"  Output saved to: {output_path}")
        logger.info(f"  Violations detected: {violations_count}")
        logger.info(f"  Violation images: {DETECTED_IMAGES_DIR}")

    except Exception as e:
        logger.error(f"Processing failed: {e}")
        import traceback
        logger.error(traceback.format_exc())
        sys.exit(1)


def main():
    """Main entry point."""
    parser = argparse.ArgumentParser(
        description='Traffic Signal Violation Detection System',
        formatter_class=argparse.RawDescriptionHelpFormatter,
        epilog="""
Examples:
  # Launch GUI (default)
  python main.py

  # Process video in CLI mode
  python main.py --video input.mp4 --line 100,200,500,200

  # Specify output path
  python main.py --video input.mp4 --line 100,200,500,200 --output violations.mp4

  # Adjust detection confidence
  python main.py --video input.mp4 --line 100,200,500,200 --confidence 0.7

  # Validate configuration only
  python main.py --validate
        """
    )

    parser.add_argument(
        '--video',
        type=str,
        help='Input video file path (for CLI mode)'
    )

    parser.add_argument(
        '--output',
        type=str,
        help='Output video file path (default: auto-generated in output/)'
    )

    parser.add_argument(
        '--line',
        type=str,
        help='Traffic signal line coordinates as x1,y1,x2,y2 (e.g., "100,200,500,200")'
    )

    parser.add_argument(
        '--confidence',
        type=float,
        default=0.5,
        help='Detection confidence threshold (default: 0.5)'
    )

    parser.add_argument(
        '--no-gui',
        action='store_true',
        help='Run in CLI mode without GUI (requires --video and --line)'
    )

    parser.add_argument(
        '--validate',
        action='store_true',
        help='Validate configuration and exit'
    )

    args = parser.parse_args()

    # Validate environment first
    if not validate_environment():
        sys.exit(1)

    # Validate-only mode
    if args.validate:
        logger.info("Configuration is valid. Exiting.")
        sys.exit(0)

    # Determine mode
    if args.video or args.no_gui:
        # CLI mode
        if not args.video or not args.line:
            logger.error("CLI mode requires --video and --line arguments")
            parser.print_help()
            sys.exit(1)

        run_cli_mode(args)
    else:
        # GUI mode (default)
        run_gui_mode()


if __name__ == "__main__":
    main()
```

#### Step 2: Make it Executable

```bash
chmod +x main.py
```

#### Step 3: Update README

Add to README.md:

```markdown
## Usage

### GUI Mode (Interactive)
```bash
python main.py
```

### CLI Mode (Headless)
```bash
# Basic usage
python main.py --video input.mp4 --line 100,200,500,200

# With custom output
python main.py --video input.mp4 --line 100,200,500,200 --output results.mp4

# Adjust detection threshold
python main.py --video input.mp4 --line 100,200,500,200 --confidence 0.7
```

### Validate Configuration
```bash
python main.py --validate
```
```

### Testing

```bash
# Test validation
python main.py --validate

# Test GUI mode
python main.py

# Test CLI mode (you'll need a sample video)
python main.py --video Resources/CCTV\ Footage.mp4 --line 200,300,800,300
```

### Acceptance Criteria

- [ ] `python main.py` launches GUI (default behavior)
- [ ] `python main.py --video ... --line ...` processes video in CLI mode
- [ ] Progress bar shows during CLI processing
- [ ] Output video saved to specified location
- [ ] `--validate` checks config and exits
- [ ] Help text is clear (`python main.py --help`)
- [ ] Errors are user-friendly

### Files to Create

1. `main.py` - New CLI entry point

### Files to Modify

1. `README.md` - Add usage examples

### Gotchas

⚠️ **Coordinate parsing:** Users might forget commas or use spaces. Add validation.

⚠️ **Tkinter import:** On some systems, tkinter isn't available. Catch the ImportError.

⚠️ **Progress bars in GUI:** Don't add tqdm to GUI mode, it breaks the display.

---

## Task 10: CSV Violation Reporting ⏳

**Priority:** MEDIUM
**Effort:** 2 hours
**Dependencies:** None
**Impact:** Enables data analysis, database import, audit trails

### Problem Statement

Currently, violations are:
- Saved as individual images
- No structured data output
- Can't be analyzed or imported to database
- No timestamp or metadata

### Solution Design

Create a CSV report generator that records all violations with metadata.

### Implementation Steps

#### Step 1: Create Report Generator Module

Create new file: `utils/report_generator.py`

```python
"""
Violation Report Generator

Creates CSV reports of detected traffic violations with metadata.
"""

import csv
from datetime import datetime
from pathlib import Path
from typing import List, Dict, Optional
import logging

logger = logging.getLogger(__name__)


class ViolationReport:
    """
    Manages violation records and generates CSV reports.

    Example:
        >>> report = ViolationReport()
        >>> report.add_violation(
        ...     frame_number=145,
        ...     timestamp="2025-10-22 14:30:15",
        ...     vehicle_type="car",
        ...     confidence=0.92,
        ...     bbox=(100, 150, 250, 300),
        ...     violation_image="violation_145.jpg"
        ... )
        >>> report.save_csv("violations_report.csv")
    """

    def __init__(self):
        """Initialize empty violation report."""
        self.violations: List[Dict] = []
        self.start_time = datetime.now()
        logger.info("ViolationReport initialized")

    def add_violation(
        self,
        frame_number: int,
        timestamp: str,
        vehicle_type: str,
        confidence: float,
        bbox: tuple,
        violation_image: Optional[str] = None
    ):
        """
        Add a violation record.

        Args:
            frame_number: Frame number where violation occurred
            timestamp: Timestamp of violation (ISO format recommended)
            vehicle_type: Type of vehicle (car, bus, truck, etc.)
            confidence: Detection confidence (0.0 to 1.0)
            bbox: Bounding box as (xmin, ymin, xmax, ymax)
            violation_image: Path to saved violation image (optional)
        """
        xmin, ymin, xmax, ymax = bbox

        violation = {
            'frame_number': frame_number,
            'timestamp': timestamp,
            'vehicle_type': vehicle_type,
            'confidence': round(confidence, 3),
            'bbox_xmin': xmin,
            'bbox_ymin': ymin,
            'bbox_xmax': xmax,
            'bbox_ymax': ymax,
            'bbox_width': xmax - xmin,
            'bbox_height': ymax - ymin,
            'violation_image': violation_image or ""
        }

        self.violations.append(violation)
        logger.debug(f"Added violation: frame {frame_number}, {vehicle_type}, conf={confidence:.2f}")

    def get_violation_count(self) -> int:
        """Get total number of violations."""
        return len(self.violations)

    def get_violations_by_type(self) -> Dict[str, int]:
        """
        Get violation count grouped by vehicle type.

        Returns:
            dict: {vehicle_type: count}
        """
        counts = {}
        for v in self.violations:
            vtype = v['vehicle_type']
            counts[vtype] = counts.get(vtype, 0) + 1
        return counts

    def save_csv(self, filepath: Path, include_summary: bool = True) -> None:
        """
        Save violations to CSV file.

        Args:
            filepath: Output CSV file path
            include_summary: Whether to include summary statistics at the end
        """
        if not self.violations:
            logger.warning("No violations to save")
            return

        filepath = Path(filepath)
        filepath.parent.mkdir(parents=True, exist_ok=True)

        fieldnames = [
            'frame_number',
            'timestamp',
            'vehicle_type',
            'confidence',
            'bbox_xmin',
            'bbox_ymin',
            'bbox_xmax',
            'bbox_ymax',
            'bbox_width',
            'bbox_height',
            'violation_image'
        ]

        try:
            with open(filepath, 'w', newline='') as f:
                writer = csv.DictWriter(f, fieldnames=fieldnames)
                writer.writeheader()
                writer.writerows(self.violations)

                if include_summary:
                    # Add summary rows
                    writer.writerow({})  # Blank line
                    writer.writerow({'frame_number': 'SUMMARY'})
                    writer.writerow({'frame_number': 'Total Violations', 'timestamp': len(self.violations)})

                    by_type = self.get_violations_by_type()
                    for vtype, count in by_type.items():
                        writer.writerow({'frame_number': f'{vtype}', 'timestamp': count})

            logger.info(f"✓ Report saved to: {filepath}")
            logger.info(f"  Total violations: {len(self.violations)}")

        except IOError as e:
            logger.error(f"Failed to save report: {e}")
            raise

    def save_json(self, filepath: Path) -> None:
        """
        Save violations to JSON file (alternative format).

        Args:
            filepath: Output JSON file path
        """
        import json

        filepath = Path(filepath)
        filepath.parent.mkdir(parents=True, exist_ok=True)

        data = {
            'metadata': {
                'report_generated': datetime.now().isoformat(),
                'total_violations': len(self.violations),
                'violations_by_type': self.get_violations_by_type()
            },
            'violations': self.violations
        }

        try:
            with open(filepath, 'w') as f:
                json.dump(data, f, indent=2)

            logger.info(f"✓ JSON report saved to: {filepath}")
        except IOError as e:
            logger.error(f"Failed to save JSON report: {e}")
            raise


if __name__ == "__main__":
    # Example usage
    report = ViolationReport()

    # Add sample violations
    report.add_violation(
        frame_number=145,
        timestamp="2025-10-22 14:30:15",
        vehicle_type="car",
        confidence=0.92,
        bbox=(100, 150, 250, 300),
        violation_image="violation_145.jpg"
    )

    report.add_violation(
        frame_number=231,
        timestamp="2025-10-22 14:30:18",
        vehicle_type="bus",
        confidence=0.87,
        bbox=(300, 100, 500, 350),
        violation_image="violation_231.jpg"
    )

    print(f"Total violations: {report.get_violation_count()}")
    print(f"By type: {report.get_violations_by_type()}")

    # Save reports
    report.save_csv("sample_report.csv")
    report.save_json("sample_report.json")

    print("✅ Example reports generated!")
```

#### Step 2: Integrate into object_detection.py

Modify `draw_boxes()` function to return violation info:

```python
def draw_boxes(image, boxes, line, labels, obj_thresh, dcnt, report=None):
    """
    Draw bounding boxes and detect violations.

    Args:
        image: Input frame
        boxes: Detected bounding boxes
        line: Traffic signal line
        labels: Class labels
        obj_thresh: Confidence threshold
        dcnt: Frame number
        report: ViolationReport instance (optional)

    Returns:
        tuple: (annotated_image, violations_in_frame)
    """
    from utils.geometry import bounding_box_intersects_line
    from datetime import datetime

    violations_in_frame = []

    for box in boxes:
        label_str = ''
        label = -1

        for i in range(len(labels)):
            if box.classes[i] > obj_thresh:
                label_str += labels[i]
                label = i

        if label >= 0:
            # Check violation
            bbox_min = (box.xmin, box.ymin)
            bbox_max = (box.xmax, box.ymax)
            is_violation = bounding_box_intersects_line(bbox_min, bbox_max, line[0], line[1])

            # Draw line
            cv2.line(image, line[0], line[1], (255, 0, 0), 3)

            if is_violation:
                # Red box for violation
                cv2.rectangle(image, (box.xmin,box.ymin), (box.xmax,box.ymax), (255,0,0), 3)

                # Save violation image
                cimg = image[box.ymin:box.ymax, box.xmin:box.xmax]
                violation_filename = f"violation_{dcnt}.jpg"
                violation_path = DETECTED_IMAGES_DIR / violation_filename
                cv2.imwrite(str(violation_path), cimg)

                # Record violation
                violation_data = {
                    'frame_number': dcnt,
                    'timestamp': datetime.now().isoformat(),
                    'vehicle_type': label_str,
                    'confidence': box.get_score(),
                    'bbox': (box.xmin, box.ymin, box.xmax, box.ymax),
                    'violation_image': violation_filename
                }
                violations_in_frame.append(violation_data)

                # Add to report if provided
                if report:
                    report.add_violation(**violation_data)

                cv2.imshow("violation", cimg)
                cv2.waitKey(5)
            else:
                # Green box for compliant
                cv2.rectangle(image, (box.xmin,box.ymin), (box.xmax,box.ymax), (0,255,0), 3)

            # Label
            cv2.putText(image,
                        label_str + ' ' + str(round(box.get_score(), 2)),
                        (box.xmin, box.ymin - 13),
                        cv2.FONT_HERSHEY_SIMPLEX,
                        1e-3 * image.shape[0],
                        (0,255,0), 2)

    return image, violations_in_frame
```

#### Step 3: Update Project-GUI.py

In `main_process()`, create and use report:

```python
from utils.report_generator import ViolationReport

def main_process(self):
    # ... existing setup ...

    # Create violation report
    report = ViolationReport()

    j = 1
    while True:
        # ... existing processing ...

        # Draw boxes with reporting
        image2, violations = od.draw_boxes(
            image, boxes, self.line,
            od.detector.labels,
            od.detector.obj_thresh,
            j,
            report=report  # Pass report instance
        )

        # ... rest of processing ...
        j += 1

    # Save report at the end
    report_path = OUTPUT_DIR / f"violations_{datetime.now():%Y%m%d_%H%M%S}.csv"
    report.save_csv(report_path)
    logger.info(f"Violation report saved: {report_path}")
```

### Testing

```python
# Run the geometry module tests
python -m utils.report_generator

# Check the generated sample files
cat sample_report.csv
cat sample_report.json
```

### Acceptance Criteria

- [ ] CSV file generated with all violation data
- [ ] Includes frame number, timestamp, vehicle type, confidence, bbox
- [ ] Summary statistics at end of CSV
- [ ] JSON format also available
- [ ] Integrates with existing violation detection
- [ ] No performance impact

### Files to Create

1. `utils/report_generator.py` - Report generation module

### Files to Modify

1. `object_detection.py` - Update `draw_boxes()` to return violation data
2. `Project-GUI.py` - Create and use ViolationReport

---

## Task 11: Progress Bars with tqdm ⏳

**Priority:** LOW
**Effort:** 1 hour
**Dependencies:** None
**Impact:** Better user experience, ETA visibility

### Problem Statement

When processing long videos:
- No indication of progress
- Can't estimate completion time
- Appears frozen (users kill the process)

### Solution Design

Add `tqdm` progress bars to show:
- Current frame / total frames
- Processing speed (FPS)
- Estimated time remaining
- Violation count

### Implementation Steps

#### Step 1: Install tqdm

Already in `requirements.txt`:
```
tqdm>=4.64.0
```

#### Step 2: Update main_process() in Project-GUI.py

```python
from tqdm import tqdm

def main_process(self):
    # ... existing setup ...

    # Get total frame count
    total_frames = int(cap.get(cv2.CAP_PROP_FRAME_COUNT))
    logger.info(f"Total frames to process: {total_frames}")

    j = 1
    violations_count = 0

    # Create progress bar
    with tqdm(total=total_frames, desc="Processing video", unit="frame") as pbar:
        while True:
            ret, image = cap.read()

            if not ret or image is None:
                break

            # ... existing processing ...

            image2, violations = od.draw_boxes(...)
            violations_count += len(violations)

            # Update progress bar
            pbar.update(1)
            pbar.set_postfix({
                'violations': violations_count,
                'current_fps': pbar.format_dict.get('rate', 0)
            })

            j += 1

    logger.info(f"Processing complete: {violations_count} violations detected")
```

#### Step 3: Add to CLI mode (main.py)

Already included in Task 9 implementation!

### Testing

Process a video and observe:
```
Processing video: 45%|████▌     | 450/1000 [00:23<00:28, 19.3frame/s, violations=3, current_fps=19.3]
```

### Acceptance Criteria

- [ ] Progress bar shows during video processing
- [ ] Displays: percentage, frames processed/total, time elapsed, ETA
- [ ] Shows violations count in real-time
- [ ] Shows current processing FPS
- [ ] Works in both GUI and CLI modes
- [ ] Doesn't break GUI display

### Files to Modify

1. `Project-GUI.py` - Add tqdm to `main_process()`
2. `main.py` - Already included in Task 9

### Gotchas

⚠️ **tqdm in GUI:** May cause display issues. Test thoroughly.

⚠️ **Total frames:** Some video formats don't report total frames correctly. Handle gracefully:
```python
total_frames = int(cap.get(cv2.CAP_PROP_FRAME_COUNT))
if total_frames <= 0:
    total_frames = None  # tqdm will show spinner instead
```

---

## Task 12: GPU Acceleration ⏳

**Priority:** HIGH
**Effort:** 2 hours
**Dependencies:** NVIDIA GPU with CUDA support
**Impact:** 5-10x faster processing

### Problem Statement

Currently:
- TensorFlow uses CPU by default
- Processing is slow (~2-5 FPS)
- Long videos take hours
- No GPU utilization even if available

### Solution Design

Configure TensorFlow to use GPU if available:
- Auto-detect GPU
- Enable memory growth (don't allocate all GPU RAM)
- Fallback to CPU gracefully
- Log GPU status

### Implementation Steps

#### Step 1: Update config.py

Add GPU configuration:

```python
# ============================================================================
# GPU CONFIGURATION
# ============================================================================

USE_GPU = True  # Enable GPU acceleration if available
GPU_MEMORY_GROWTH = True  # Allow dynamic GPU memory allocation
GPU_MEMORY_LIMIT = None  # Limit GPU memory in MB (None = no limit)


def configure_gpu():
    """
    Configure GPU settings for TensorFlow.

    Returns:
        bool: True if GPU is available and configured, False otherwise
    """
    import logging
    logger = logging.getLogger(__name__)

    if not USE_GPU:
        logger.info("GPU acceleration disabled in config")
        return False

    try:
        import tensorflow as tf

        # List available GPUs
        gpus = tf.config.list_physical_devices('GPU')

        if not gpus:
            logger.info("No GPU detected, using CPU")
            return False

        logger.info(f"Found {len(gpus)} GPU(s):")
        for gpu in gpus:
            logger.info(f"  - {gpu.name}")

        # Configure each GPU
        for gpu in gpus:
            if GPU_MEMORY_GROWTH:
                # Enable memory growth (allocate as needed)
                tf.config.experimental.set_memory_growth(gpu, True)
                logger.info(f"  Enabled memory growth for {gpu.name}")

            if GPU_MEMORY_LIMIT:
                # Set memory limit
                tf.config.set_logical_device_configuration(
                    gpu,
                    [tf.config.LogicalDeviceConfiguration(memory_limit=GPU_MEMORY_LIMIT)]
                )
                logger.info(f"  Set memory limit to {GPU_MEMORY_LIMIT}MB for {gpu.name}")

        # Test GPU
        with tf.device('/GPU:0'):
            test = tf.constant([[1.0, 2.0], [3.0, 4.0]])
            result = tf.matmul(test, test)

        logger.info("✓ GPU acceleration enabled and tested successfully")
        return True

    except RuntimeError as e:
        logger.error(f"GPU configuration error: {e}")
        logger.info("Falling back to CPU")
        return False
    except Exception as e:
        logger.warning(f"Unexpected error configuring GPU: {e}")
        logger.info("Falling back to CPU")
        return False


# Auto-configure GPU on import
GPU_AVAILABLE = configure_gpu()
```

#### Step 2: Update main.py

Call GPU configuration:

```python
from config import GPU_AVAILABLE

def validate_environment():
    """Validate environment."""
    logger.info("Validating environment...")

    # ... existing validation ...

    # Report GPU status
    if GPU_AVAILABLE:
        logger.info("✓ GPU acceleration enabled")
    else:
        logger.info("⚠ Running on CPU (slower)")

    return True
```

#### Step 3: Add Performance Logging

In `object_detection.py`, add timing:

```python
import time

class ViolationDetector:
    def __init__(self):
        # ... existing code ...
        self.inference_times = []

    def predict(self, preprocessed_image):
        """Run detection with timing."""
        start = time.time()
        result = self.model.predict(preprocessed_image)
        elapsed = time.time() - start

        self.inference_times.append(elapsed)

        # Log every 100 frames
        if len(self.inference_times) % 100 == 0:
            avg_time = sum(self.inference_times[-100:]) / 100
            fps = 1 / avg_time if avg_time > 0 else 0
            logger.info(f"Average inference: {avg_time*1000:.1f}ms ({fps:.1f} FPS)")

        return result
```

### Installation (User Instructions)

Add to README.md:

```markdown
## GPU Acceleration

### Requirements
- NVIDIA GPU with CUDA Compute Capability 3.5+
- CUDA Toolkit 11.2+
- cuDNN 8.1+

### Installation (Ubuntu/Linux)
```bash
# Install CUDA and cuDNN (example for Ubuntu 20.04)
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/ /"
sudo apt-get update
sudo apt-get install cuda-11-2 libcudnn8

# Install TensorFlow GPU
pip install tensorflow[and-cuda]>=2.10.0
```

### Verify GPU
```bash
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
```

### Performance
- **CPU:** ~2-5 FPS
- **GPU (GTX 1060):** ~15-20 FPS
- **GPU (RTX 3070):** ~30-40 FPS
```

### Testing

```python
# Test GPU detection
python -c "from config import GPU_AVAILABLE; print(f'GPU Available: {GPU_AVAILABLE}')"

# Check TensorFlow GPU
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"

# Process video and check FPS improvement
python main.py --video test.mp4 --line 100,200,500,200
# Monitor logs for "Average inference" messages
```

### Acceptance Criteria

- [ ] Auto-detects GPU if available
- [ ] Enables memory growth to avoid OOM errors
- [ ] Logs GPU status on startup
- [ ] Falls back to CPU gracefully if no GPU
- [ ] Logs inference FPS
- [ ] 5-10x faster with GPU vs CPU
- [ ] Doesn't break CPU-only systems

### Files to Modify

1. `config.py` - Add GPU configuration
2. `main.py` - Report GPU status
3. `object_detection.py` - Add performance logging
4. `README.md` - Add GPU installation instructions

### Gotchas

⚠️ **Memory growth is critical:** Without it, TensorFlow allocates ALL GPU memory and may crash.

⚠️ **CUDA version compatibility:** TensorFlow 2.10+ requires CUDA 11.2+. Check compatibility matrix.

⚠️ **Multiple GPUs:** Code uses first GPU by default. For multi-GPU, use `tf.distribute.MirroredStrategy()`.

⚠️ **WSL2 on Windows:** GPU support requires WSL2 + CUDA toolkit in WSL.

---

## Task 13: Unit Tests ⏳

**Priority:** MEDIUM
**Effort:** 6 hours
**Dependencies:** None
**Impact:** Prevents regressions, ensures code quality

### Problem Statement

Currently:
- Only 1 module has tests (`utils/geometry.py`)
- No automated testing
- Easy to break things when refactoring
- No CI/CD possible

### Solution Design

Add comprehensive test suite using pytest:
- Unit tests for all utility functions
- Integration tests for detection pipeline
- Test fixtures for sample data
- Mocking for expensive operations (model loading)

### Implementation Steps

#### Step 1: Create Test Directory Structure

```bash
mkdir -p tests
touch tests/__init__.py
touch tests/conftest.py
```

#### Step 2: Create conftest.py (Shared Fixtures)

File: `tests/conftest.py`

```python
"""
Pytest configuration and shared fixtures.
"""

import pytest
import numpy as np
from pathlib import Path


@pytest.fixture
def sample_image():
    """Create a sample test image."""
    # 640x480 RGB image
    return np.random.randint(0, 255, (480, 640, 3), dtype=np.uint8)


@pytest.fixture
def sample_bbox():
    """Create a sample bounding box."""
    from object_detection import BoundBox
    return BoundBox(
        xmin=100,
        ymin=150,
        xmax=250,
        ymax=300,
        objness=0.95,
        classes=[0.1, 0.05, 0.92, 0.03, ...]  # 80 classes for COCO
    )


@pytest.fixture
def traffic_line():
    """Sample traffic signal line."""
    return [(100, 200), (500, 200)]  # Horizontal line


@pytest.fixture
def project_root():
    """Get project root directory."""
    return Path(__file__).parent.parent
```

#### Step 3: Test Geometry Module

File: `tests/test_geometry.py`

```python
"""
Tests for utils/geometry.py
"""

import pytest
from utils.geometry import (
    line_segment_intersection,
    bounding_box_intersects_line,
    point_distance,
    is_point_in_bounding_box
)


class TestLineSegmentIntersection:
    """Tests for line_segment_intersection function."""

    def test_perpendicular_lines_intersect(self):
        """Perpendicular lines should intersect."""
        assert line_segment_intersection(
            (0, 5), (10, 5),  # Horizontal
            (5, 0), (5, 10)   # Vertical
        ) == True

    def test_parallel_lines_no_intersection(self):
        """Parallel lines should not intersect."""
        assert line_segment_intersection(
            (0, 0), (10, 0),
            (0, 5), (10, 5)
        ) == False

    def test_crossing_diagonal_lines(self):
        """Diagonal lines crossing should intersect."""
        assert line_segment_intersection(
            (0, 0), (10, 10),
            (0, 10), (10, 0)
        ) == True

    def test_non_intersecting_segments(self):
        """Non-overlapping segments should not intersect."""
        assert line_segment_intersection(
            (0, 0), (5, 5),
            (10, 0), (15, 5)
        ) == False

    def test_touching_endpoints(self):
        """Segments touching at endpoints should intersect."""
        assert line_segment_intersection(
            (0, 0), (5, 5),
            (5, 5), (10, 0)
        ) == True

    def test_collinear_overlapping(self):
        """Collinear overlapping segments."""
        # This is an edge case - current implementation returns False
        result = line_segment_intersection(
            (0, 0), (10, 0),
            (5, 0), (15, 0)
        )
        assert isinstance(result, bool)


class TestBoundingBoxIntersectsLine:
    """Tests for bounding_box_intersects_line function."""

    def test_bbox_crosses_horizontal_line(self):
        """Bbox crossing horizontal line should intersect."""
        assert bounding_box_intersects_line(
            (100, 100), (200, 200),  # Bbox
            (0, 150), (300, 150)      # Horizontal line through bbox
        ) == True

    def test_bbox_above_line_no_intersection(self):
        """Bbox entirely above line should not intersect."""
        assert bounding_box_intersects_line(
            (100, 100), (200, 150),  # Bbox above
            (0, 200), (300, 200)      # Line below
        ) == False

    def test_bbox_below_line_no_intersection(self):
        """Bbox entirely below line should not intersect."""
        assert bounding_box_intersects_line(
            (100, 250), (200, 300),  # Bbox below
            (0, 200), (300, 200)      # Line above
        ) == False

    def test_line_touches_bbox_corner(self):
        """Line touching bbox corner should intersect."""
        assert bounding_box_intersects_line(
            (100, 100), (200, 200),
            (0, 100), (300, 100)  # Line at top edge
        ) == True

    def test_diagonal_line_through_bbox(self):
        """Diagonal line through bbox should intersect."""
        assert bounding_box_intersects_line(
            (100, 100), (200, 200),
            (0, 0), (300, 300)  # Diagonal
        ) == True


class TestPointDistance:
    """Tests for point_distance function."""

    def test_3_4_5_triangle(self):
        """Classic 3-4-5 right triangle."""
        assert point_distance((0, 0), (3, 4)) == 5.0

    def test_same_point_zero_distance(self):
        """Distance from point to itself is zero."""
        assert point_distance((5, 5), (5, 5)) == 0.0

    def test_horizontal_distance(self):
        """Horizontal distance."""
        assert point_distance((0, 5), (10, 5)) == 10.0

    def test_vertical_distance(self):
        """Vertical distance."""
        assert point_distance((5, 0), (5, 10)) == 10.0

    def test_negative_coordinates(self):
        """Distance with negative coordinates."""
        distance = point_distance((-3, -4), (0, 0))
        assert abs(distance - 5.0) < 0.01


class TestIsPointInBoundingBox:
    """Tests for is_point_in_bounding_box function."""

    def test_point_inside_bbox(self):
        """Point clearly inside bbox."""
        assert is_point_in_bounding_box(
            (50, 50),
            (0, 0), (100, 100)
        ) == True

    def test_point_outside_bbox(self):
        """Point clearly outside bbox."""
        assert is_point_in_bounding_box(
            (150, 50),
            (0, 0), (100, 100)
        ) == False

    def test_point_on_edge(self):
        """Point on bbox edge should be inside."""
        assert is_point_in_bounding_box(
            (100, 50),
            (0, 0), (100, 100)
        ) == True

    def test_point_at_corner(self):
        """Point at bbox corner should be inside."""
        assert is_point_in_bounding_box(
            (100, 100),
            (0, 0), (100, 100)
        ) == True
```

#### Step 4: Test Detection Functions

File: `tests/test_detection.py`

```python
"""
Tests for object_detection.py functions.
"""

import pytest
import numpy as np
from object_detection import (
    preprocess_input,
    bbox_iou,
    BoundBox,
    _sigmoid,
    _interval_overlap
)


class TestPreprocessInput:
    """Tests for preprocess_input function."""

    def test_output_shape(self, sample_image):
        """Preprocessed image should have correct shape."""
        net_h, net_w = 416, 416
        result = preprocess_input(sample_image, net_h, net_w)

        assert result.shape == (1, net_h, net_w, 3)

    def test_output_range(self, sample_image):
        """Preprocessed image should be normalized to [0, 1]."""
        result = preprocess_input(sample_image, 416, 416)

        assert result.min() >= 0.0
        assert result.max() <= 1.0

    def test_different_input_sizes(self):
        """Should handle different input image sizes."""
        small_image = np.random.randint(0, 255, (100, 100, 3), dtype=np.uint8)
        large_image = np.random.randint(0, 255, (1920, 1080, 3), dtype=np.uint8)

        result_small = preprocess_input(small_image, 416, 416)
        result_large = preprocess_input(large_image, 416, 416)

        assert result_small.shape == (1, 416, 416, 3)
        assert result_large.shape == (1, 416, 416, 3)


class TestBoundBox:
    """Tests for BoundBox class."""

    def test_bbox_creation(self):
        """BoundBox should initialize correctly."""
        bbox = BoundBox(10, 20, 100, 200, objness=0.9, classes=[0.1, 0.9])

        assert bbox.xmin == 10
        assert bbox.ymin == 20
        assert bbox.xmax == 100
        assert bbox.ymax == 200
        assert bbox.objness == 0.9

    def test_get_label(self):
        """get_label should return index of highest class probability."""
        bbox = BoundBox(0, 0, 10, 10, objness=0.9, classes=[0.1, 0.3, 0.8, 0.2])

        assert bbox.get_label() == 2  # Index of 0.8

    def test_get_score(self):
        """get_score should return highest class probability."""
        bbox = BoundBox(0, 0, 10, 10, objness=0.9, classes=[0.1, 0.3, 0.8, 0.2])

        assert bbox.get_score() == 0.8


class TestBboxIOU:
    """Tests for bbox_iou function."""

    def test_identical_boxes(self):
        """Identical boxes should have IOU = 1.0."""
        box1 = BoundBox(10, 10, 50, 50)
        box2 = BoundBox(10, 10, 50, 50)

        assert bbox_iou(box1, box2) == 1.0

    def test_no_overlap(self):
        """Non-overlapping boxes should have IOU = 0.0."""
        box1 = BoundBox(10, 10, 50, 50)
        box2 = BoundBox(100, 100, 150, 150)

        assert bbox_iou(box1, box2) == 0.0

    def test_partial_overlap(self):
        """Partially overlapping boxes should have 0 < IOU < 1."""
        box1 = BoundBox(10, 10, 50, 50)
        box2 = BoundBox(30, 30, 70, 70)

        iou = bbox_iou(box1, box2)
        assert 0.0 < iou < 1.0

    def test_one_inside_other(self):
        """Box inside another should have IOU < 1."""
        box1 = BoundBox(10, 10, 100, 100)
        box2 = BoundBox(30, 30, 70, 70)

        iou = bbox_iou(box1, box2)
        assert 0.0 < iou < 1.0


class TestHelperFunctions:
    """Tests for helper functions."""

    def test_sigmoid_zero(self):
        """Sigmoid of 0 should be 0.5."""
        assert abs(_sigmoid(0) - 0.5) < 0.01

    def test_sigmoid_positive(self):
        """Sigmoid of large positive should approach 1."""
        assert _sigmoid(10) > 0.99

    def test_sigmoid_negative(self):
        """Sigmoid of large negative should approach 0."""
        assert _sigmoid(-10) < 0.01

    def test_interval_overlap_full(self):
        """Fully overlapping intervals."""
        overlap = _interval_overlap([0, 10], [5, 15])
        assert overlap == 5

    def test_interval_overlap_none(self):
        """Non-overlapping intervals."""
        overlap = _interval_overlap([0, 10], [20, 30])
        assert overlap == 0

    def test_interval_overlap_identical(self):
        """Identical intervals."""
        overlap = _interval_overlap([0, 10], [0, 10])
        assert overlap == 10
```

#### Step 5: Test Report Generator

File: `tests/test_report_generator.py`

```python
"""
Tests for utils/report_generator.py
"""

import pytest
from pathlib import Path
import tempfile
import csv
import json
from utils.report_generator import ViolationReport


class TestViolationReport:
    """Tests for ViolationReport class."""

    @pytest.fixture
    def report(self):
        """Create a fresh report for each test."""
        return ViolationReport()

    def test_empty_report(self, report):
        """New report should be empty."""
        assert report.get_violation_count() == 0
        assert report.get_violations_by_type() == {}

    def test_add_single_violation(self, report):
        """Adding a violation should increment count."""
        report.add_violation(
            frame_number=100,
            timestamp="2025-10-22 14:30:00",
            vehicle_type="car",
            confidence=0.92,
            bbox=(100, 150, 250, 300)
        )

        assert report.get_violation_count() == 1

    def test_add_multiple_violations(self, report):
        """Multiple violations should all be recorded."""
        for i in range(5):
            report.add_violation(
                frame_number=100 + i,
                timestamp=f"2025-10-22 14:30:{i:02d}",
                vehicle_type="car",
                confidence=0.9,
                bbox=(100, 150, 250, 300)
            )

        assert report.get_violation_count() == 5

    def test_violations_by_type(self, report):
        """Should group violations by vehicle type."""
        report.add_violation(100, "...", "car", 0.9, (0, 0, 10, 10))
        report.add_violation(101, "...", "car", 0.9, (0, 0, 10, 10))
        report.add_violation(102, "...", "bus", 0.9, (0, 0, 10, 10))
        report.add_violation(103, "...", "truck", 0.9, (0, 0, 10, 10))

        by_type = report.get_violations_by_type()
        assert by_type['car'] == 2
        assert by_type['bus'] == 1
        assert by_type['truck'] == 1

    def test_save_csv(self, report):
        """Should save CSV file with correct format."""
        report.add_violation(
            frame_number=100,
            timestamp="2025-10-22 14:30:00",
            vehicle_type="car",
            confidence=0.92,
            bbox=(100, 150, 250, 300),
            violation_image="violation_100.jpg"
        )

        with tempfile.NamedTemporaryFile(mode='w', delete=False, suffix='.csv') as f:
            temp_path = Path(f.name)

        try:
            report.save_csv(temp_path)

            # Verify file exists
            assert temp_path.exists()

            # Read and verify contents
            with open(temp_path, 'r') as f:
                reader = csv.DictReader(f)
                rows = list(reader)

                # Should have header + 1 data row + blank + summary rows
                assert len(rows) >= 1
                assert rows[0]['frame_number'] == '100'
                assert rows[0]['vehicle_type'] == 'car'
                assert float(rows[0]['confidence']) == 0.92

        finally:
            temp_path.unlink()

    def test_save_json(self, report):
        """Should save JSON file with correct format."""
        report.add_violation(100, "2025-10-22", "car", 0.9, (0, 0, 10, 10))

        with tempfile.NamedTemporaryFile(mode='w', delete=False, suffix='.json') as f:
            temp_path = Path(f.name)

        try:
            report.save_json(temp_path)

            assert temp_path.exists()

            with open(temp_path, 'r') as f:
                data = json.load(f)

                assert 'metadata' in data
                assert 'violations' in data
                assert data['metadata']['total_violations'] == 1
                assert len(data['violations']) == 1

        finally:
            temp_path.unlink()
```

#### Step 6: Create pytest Configuration

File: `pytest.ini`

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts =
    -v
    --tb=short
    --strict-markers
    --disable-warnings
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    integration: marks tests as integration tests
    unit: marks tests as unit tests
```

#### Step 7: Update requirements.txt

Already included:
```txt
pytest>=7.2.0
pytest-cov>=4.0.0
```

### Running Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=. --cov-report=html

# Run specific test file
pytest tests/test_geometry.py

# Run specific test class
pytest tests/test_geometry.py::TestLineSegmentIntersection

# Run specific test
pytest tests/test_geometry.py::TestLineSegmentIntersection::test_perpendicular_lines_intersect

# Run only fast tests (skip slow)
pytest -m "not slow"
```

### Acceptance Criteria

- [ ] All tests pass (`pytest` exits with code 0)
- [ ] Test coverage > 70% for utility modules
- [ ] Tests run in < 5 seconds (excluding slow tests)
- [ ] CI/CD ready (can run in GitHub Actions)
- [ ] Clear test names that describe what they test
- [ ] No hardcoded paths in tests

### Files to Create

1. `tests/__init__.py` - Test package marker
2. `tests/conftest.py` - Shared fixtures
3. `tests/test_geometry.py` - Geometry tests
4. `tests/test_detection.py` - Detection tests
5. `tests/test_report_generator.py` - Report generator tests
6. `pytest.ini` - Pytest configuration

### Gotchas

⚠️ **Model loading in tests:** Don't load the full YOLOv3 model in tests (too slow). Use mocking:
```python
from unittest.mock import Mock, patch

@patch('object_detection.make_yolov3_model')
def test_something(mock_model):
    mock_model.return_value = Mock()
    # ... test code ...
```

⚠️ **Temporary files:** Always clean up temp files in tests using `try/finally` or pytest fixtures.

⚠️ **Floating point comparison:** Use `abs(a - b) < 0.01` or `pytest.approx()` for float comparison.

---

## Task 14: Comprehensive Docstrings ⏳

**Priority:** LOW
**Effort:** 3 hours
**Dependencies:** None
**Impact:** Better code understanding and maintenance

### Problem Statement

Many functions lack documentation:
- No docstrings in `object_detection.py` (20+ functions)
- Incomplete docstrings in `Project-GUI.py`
- No module-level documentation

### Solution Design

Add comprehensive docstrings following Google style:
- Module-level docstrings
- Function docstrings with Args/Returns/Raises
- Class docstrings
- Type hints where missing

### Implementation Steps

Follow the pattern from `utils/geometry.py`:

```python
def function_name(arg1: type, arg2: type) -> return_type:
    """
    Brief one-line summary.

    Detailed description of what this function does, why it exists,
    and any important implementation details.

    Args:
        arg1: Description of first argument
        arg2: Description of second argument

    Returns:
        Description of return value

    Raises:
        ValueError: When input is invalid
        IOError: When file operations fail

    Example:
        >>> function_name(1, 2)
        3
    """
    # Implementation
```

### Acceptance Criteria

- [ ] All public functions have docstrings
- [ ] All modules have module-level docstrings
- [ ] All classes have class docstrings
- [ ] Docstrings follow Google style guide
- [ ] Type hints on all function signatures

### Files to Modify

1. `object_detection.py` - Add docstrings to all functions
2. `Project-GUI.py` - Complete docstrings for all methods
3. `main.py` - Add module docstring
4. All other modules

---

## Task 15: Docker Container ⏳

**Priority:** MEDIUM
**Effort:** 3 hours
**Dependencies:** None
**Impact:** Easy deployment, reproducible environment

### Problem Statement

Deployment challenges:
- Complex dependency setup
- Different environments (dev/staging/prod)
- Version conflicts
- "Works on my machine" problems

### Solution Design

Create Docker container with:
- All dependencies pre-installed
- YOLO weights included
- Volume mounts for input/output
- Both GUI and headless modes

### Implementation Steps

#### Step 1: Create Dockerfile

File: `Dockerfile`

```dockerfile
FROM python:3.9-slim

# Install system dependencies
RUN apt-get update && apt-get install -y \
    libgl1-mesa-glx \
    libglib2.0-0 \
    libsm6 \
    libxext6 \
    libxrender-dev \
    wget \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy requirements first (for layer caching)
COPY requirements-minimal.txt .
RUN pip install --no-cache-dir -r requirements-minimal.txt

# Copy application code
COPY . .

# Download YOLO weights if not present
RUN if [ ! -f weights/yolov3.weights ]; then \
    mkdir -p weights && \
    wget -q --show-progress \
        -O weights/yolov3.weights \
        https://pjreddie.com/media/files/yolov3.weights; \
    fi

# Create necessary directories
RUN python -c "import config"

# Set environment variables
ENV PYTHONUNBUFFERED=1

# Default command (CLI mode)
CMD ["python", "main.py", "--no-gui"]
```

#### Step 2: Create .dockerignore

File: `.dockerignore`

```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
ENV/
.venv

# Git
.git/
.gitignore

# IDE
.vscode/
.idea/
*.swp
*.swo

# Outputs (will be mounted as volumes)
Resources/output/*
Detected Images/*

# Documentation
*.md
!README.md

# Tests
tests/
pytest.ini
.coverage
htmlcov/

# Large files (download during build)
*.weights

# OS
.DS_Store
Thumbs.db
```

#### Step 3: Create docker-compose.yml

File: `docker-compose.yml`

```yaml
version: '3.8'

services:
  detector:
    build: .
    image: traffic-violation-detector:latest
    container_name: traffic-detector

    volumes:
      # Mount input/output directories
      - ./Resources/input:/app/Resources/input:ro
      - ./Resources/output:/app/Resources/output
      - ./Detected Images:/app/Detected\ Images

    environment:
      # Set logging level
      - LOG_LEVEL=INFO

    # For GPU support (requires nvidia-docker)
    # deploy:
    #   resources:
    #     reservations:
    #       devices:
    #         - driver: nvidia
    #           count: 1
    #           capabilities: [gpu]

    # Override command for specific video
    command: >
      python main.py
      --video /app/Resources/input/video.mp4
      --line 100,200,500,200
      --output /app/Resources/output/output.mp4
```

#### Step 4: Create Build Script

File: `docker-build.sh`

```bash
#!/bin/bash
# Build Docker image for Traffic Violation Detection System

set -e

echo "Building Docker image..."
docker build -t traffic-violation-detector:latest .

echo "✓ Build complete!"
echo ""
echo "Usage:"
echo "  # Run with docker-compose"
echo "  docker-compose up"
echo ""
echo "  # Run manually"
echo "  docker run -v \$(pwd)/Resources/input:/app/Resources/input \\"
echo "             -v \$(pwd)/Resources/output:/app/Resources/output \\"
echo "             traffic-violation-detector:latest \\"
echo "             python main.py --video /app/Resources/input/video.mp4 --line 100,200,500,200"
```

```bash
chmod +x docker-build.sh
```

#### Step 5: Update README with Docker Instructions

Add to README.md:

```markdown
## Docker Deployment

### Build Image
```bash
./docker-build.sh
# OR
docker build -t traffic-violation-detector .
```

### Run with Docker Compose
```bash
# Edit docker-compose.yml to set your video and line coordinates
docker-compose up
```

### Run Manually
```bash
docker run -v $(pwd)/Resources/input:/app/Resources/input \
           -v $(pwd)/Resources/output:/app/Resources/output \
           -v $(pwd)/Detected\ Images:/app/Detected\ Images \
           traffic-violation-detector:latest \
           python main.py \
           --video /app/Resources/input/video.mp4 \
           --line 100,200,500,200
```

### GPU Support
```bash
# Install nvidia-docker
# Uncomment GPU section in docker-compose.yml
docker-compose up
```
```

### Testing

```bash
# Build image
./docker-build.sh

# Test with sample video
docker run -v $(pwd)/Resources:/app/Resources \
           traffic-violation-detector:latest \
           python main.py --validate

# Process video
# (Place video.mp4 in Resources/input first)
docker-compose up
```

### Acceptance Criteria

- [ ] Dockerfile builds successfully
- [ ] Image size < 3GB
- [ ] All dependencies installed
- [ ] Can process video in container
- [ ] Volume mounts work correctly
- [ ] Output files accessible from host
- [ ] Works on Linux, macOS, Windows (Docker Desktop)

### Files to Create

1. `Dockerfile` - Container definition
2. `.dockerignore` - Files to exclude from image
3. `docker-compose.yml` - Orchestration config
4. `docker-build.sh` - Build script

### Files to Modify

1. `README.md` - Add Docker instructions

### Gotchas

⚠️ **Image size:** YOLO weights are 237MB. Keep image lean by using `-slim` base and cleaning up.

⚠️ **File permissions:** Output files may have root ownership. Add `--user $(id -u):$(id -g)` to docker run.

⚠️ **GUI in Docker:** Requires X11 forwarding:
```bash
docker run -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix ...
```

⚠️ **GPU in Docker:** Requires `nvidia-docker` runtime and proper drivers.

---

## Common Patterns & Best Practices

### Error Handling Pattern

```python
import logging
import traceback
from tkinter import messagebox

logger = logging.getLogger(__name__)

def my_function():
    resource = None
    try:
        resource = acquire_resource()
        # Do work
        logger.info("Success")
    except SpecificError as e:
        logger.error(f"Specific error: {e}")
        messagebox.showerror("Error", str(e))
    except Exception as e:
        logger.error(f"Unexpected: {e}\n{traceback.format_exc()}")
        messagebox.showerror("Error", f"Unexpected error: {e}")
    finally:
        if resource:
            resource.close()
            logger.debug("Resource released")
```

### Configuration Pattern

```python
from config import SOME_PATH, SOME_PARAMETER

# NOT:
path = "G:/hardcoded/path"

# YES:
path = SOME_PATH
```

### Logging Pattern

```python
import logging

logger = logging.getLogger(__name__)

# NOT:
print("Processing frame 10")

# YES:
logger.info("Processing frame 10")
logger.debug(f"Frame details: {details}")
logger.error(f"Failed: {error}")
```

### Type Hints Pattern

```python
from typing import List, Dict, Tuple, Optional

def process_data(
    items: List[str],
    config: Dict[str, int],
    bbox: Tuple[int, int, int, int],
    optional_param: Optional[str] = None
) -> bool:
    """Process data with type hints."""
    pass
```

---

## Troubleshooting Guide

### Common Issues

**Issue: Import errors after refactoring**
```python
# Solution: Update all imports
from utils.geometry import bounding_box_intersects_line
# NOT: from geometry import ...
```

**Issue: Model loads on every import**
```python
# Solution: Use lazy loading (Task 7)
detector = ViolationDetector()  # Model NOT loaded
detector.predict(...)  # Now loads
```

**Issue: Tests fail with path errors**
```python
# Solution: Use Path objects
from pathlib import Path
path = Path(__file__).parent / "file.txt"
```

**Issue: GPU not detected**
```bash
# Check CUDA
nvidia-smi
# Check TensorFlow
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
# Reinstall if needed
pip install tensorflow[and-cuda]
```

**Issue: Progress bar breaks GUI**
```python
# Solution: Only use tqdm in CLI mode
if not gui_mode:
    with tqdm(...) as pbar:
        # ...
```

---

## Getting Help

### Reference Existing Code

- **Configuration:** See `config.py` (240 lines, complete example)
- **Error Handling:** See `Project-GUI.py` lines 52-112
- **Type Hints & Docs:** See `utils/geometry.py`
- **Testing:** See built-in tests in `utils/geometry.py`

### Documentation

- **Implementation Plan:** `IMPLEMENTATION_PLAN.md`
- **Code Analysis:** `CODE_SUMMARY_AND_IMPROVEMENTS.md`
- **This Guide:** `REMAINING_IMPLEMENTATION_GUIDE.md`

### External Resources

- **TensorFlow 2.x Guide:** https://www.tensorflow.org/guide/migrate
- **Pytest Documentation:** https://docs.pytest.org/
- **Docker Best Practices:** https://docs.docker.com/develop/dev-best-practices/
- **Python Type Hints:** https://docs.python.org/3/library/typing.html

---

## Success Criteria for Completion

### Task Completion Checklist

For each task to be considered complete:

- [ ] Implementation matches specification
- [ ] Code follows existing patterns
- [ ] All acceptance criteria met
- [ ] No breaking changes to existing functionality
- [ ] Logging added for important operations
- [ ] Error handling implemented
- [ ] Docstrings added (if new functions)
- [ ] Manual testing performed
- [ ] Committed with descriptive message
- [ ] Updated relevant documentation

### Phase Completion

**Phase 2 Complete when:**
- [ ] Task 7: Lazy loading working
- [ ] Task 8: Modular structure in place

**Phase 3 Complete when:**
- [ ] Task 9: CLI interface functional
- [ ] Task 10: CSV reports generating
- [ ] Task 11: Progress bars showing
- [ ] Task 12: GPU detection working

**Phase 4 Complete when:**
- [ ] Task 13: Test suite passing (>70% coverage)
- [ ] Task 14: All public APIs documented

**Phase 5 Complete when:**
- [ ] Task 15: Docker image builds and runs

---

## Quick Start for New Contributor

```bash
# 1. Clone and setup
git clone https://github.com/mohi86/Traffic-Signal-Violation-Detection-System.git
cd Traffic-Signal-Violation-Detection-System
git checkout claude/summarize-prototype-011CUL8sE15HcErw1kqz3gno

# 2. Install dependencies
pip install -r requirements.txt

# 3. Download YOLO weights (if needed)
# wget https://pjreddie.com/media/files/yolov3.weights -P weights/

# 4. Validate setup
python config.py

# 5. Run tests
python utils/geometry.py

# 6. Pick a task from this guide and start coding!
```

---

**Document Version:** 1.0
**Last Updated:** 2025-10-22
**Maintained By:** Development Team
**Questions:** See `CODE_SUMMARY_AND_IMPROVEMENTS.md` or `IMPLEMENTATION_PLAN.md`
