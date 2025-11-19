# Copilot Instructions

## Project Overview

**Sift** is a high-performance local desktop application designed to intelligently filter and organize photo collections. Its primary goal is to reduce manual workload by automatically identifying low-quality and redundant images with high accuracy.

### Core Workflow: Group & Rank

The application follows a strict "Group & Rank" philosophy:

1.  **Grouping**: Images are first clustered based on content.
    - **Exact Duplicates**: Byte-for-byte identical files.
    - **High Similarity**: Visually similar images (e.g., burst shots) based on a user-configurable similarity threshold.
2.  **Ranking**: Within each group, images are scored and sorted by quality.
3.  **Recommendation**: The highest-scoring image in a group is marked as the "Keeper," while others are flagged for potential deletion.

### Filtering Dimensions (Quality Metrics)

The application evaluates images based on the following criteria:

- **Similarity**: Perceptual hashing (pHash/dHash) to find near-duplicates.
- **Resolution**: Flagging images with dimensions (width/height) or total pixel count below a certain threshold.
- **Focus/Clarity**: Detecting out-of-focus images (Laplacian Variance).
- **Motion Blur**: Identifying directional blur caused by camera shake or subject movement.
- **File Size**: Filtering out unusually small files (likely thumbnails or corrupted data).

### Architecture

The application utilizes a **Python bridge** (spawned as a persistent subprocess) to handle heavy image processing tasks (OpenCV, NumPy) via Node.js, ensuring the UI remains responsive while processing large datasets.

## Tech Stack

- **Runtime**: Electron.js
- **Language**: TypeScript
- **Frontend Framework**: React
- **UI Library**: shadcn/ui + Radix UI
- **Styling**: Tailwind CSS
- **State Management**: Zustand
- **Data Fetching**: TanStack Query
- **Build Tool**: Vite
- **Package Manager**: pnpm
- **Image Processing**: Python (OpenCV, NumPy, scikit-image, imagededup) via Node.js bridge

## Coding Guidelines

- Always use TypeScript for new code.
- Prefer functional components for React.
- Use 'const' instead of 'let' whenever possible.
- Keep comments in English.
