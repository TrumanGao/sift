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

The application evaluates images based on the following criteria (optionally configurable by the user):

- **Similarity**: Perceptual hashing (pHash/dHash) to find near-duplicates.
- **Resolution**: Flagging images with dimensions (width/height) or total pixel count below a certain threshold.
- **Focus/Clarity**: Detecting out-of-focus images (Laplacian Variance).
- **Motion Blur**: Identifying directional blur caused by camera shake or subject movement.
- **File Size**: Filtering out unusually small files (likely thumbnails or corrupted data).
- **Metadata Completeness**: Checking for the presence of essential EXIF data (e.g., date taken, camera model); images missing metadata may be ranked lower than identical ones with full metadata.

### Architecture

The application utilizes a **Python bridge** (spawned as a persistent subprocess) to handle heavy image processing tasks (OpenCV, NumPy) via Node.js, ensuring the UI remains responsive while processing large datasets.

### Operation Logic

1. 批量选择图片和文件夹；
2. 用户自定义评分条件和相似度阈值（即多大程度的相似视为重复）；
3. 开始执行分析图片，显示进度条和百分比，允许取消；
4. 分析结果按相似度分组，每个分组按质量评分排序其缩略图。支持全局选择是否显示图片名称、评分，图片路径、拍摄时间、修改时间、其他元数据等（默认显示图片名称及评分）；支持缩略图的缩放比例调整，方便无需点开原始图片即可快速浏览清晰图片；因为图片数量可能非常大，采用虚拟列表技术（react-window）提升渲染性能；
5. 双击缩略图全屏查看原始图片，支持左右箭头切换；
6. 用户可手动勾选/取消要删除的图片，支持拖动选择范围删除，或一键全选/反选每个分组的低质量图片；底部实时显示已选图片数量和预计释放存储空间；
7. 点击“删除选中图片”按钮，弹出确认对话框，确认后将所选图片移动到系统回收站；
8. 删除后显示统计信息，即删除图片数量和释放存储空间。

### Other Features

- 除非用户手动触发删除操作，否则绝不能对任何图片做出任何修改（包括移动、重命名、覆盖等）。该应用可能触发的唯一修改操作是将用户确认删除的图片移动到系统回收站。
- 分析过程不能阻塞主线程，UI必须保持流畅响应，允许用户随时取消分析操作。
- 为了提升分析性能，是否可以持久化存储图片的感知哈希值（pHash/dHash）和质量评分等信息，下次分析时跳过已处理的图片，仅处理新增或修改过的图片？但如何保证这些持久化信息的准确性？或许可以通过比较文件名、文件修改时间戳、文件大小等不需要分析即可获取的元数据来决定是否图片发生变更因而重新计算？
- 持久化用户的自定义设置（评分条件、相似度阈值等）。
- 用户点击退出时弹出确认对话框，防止误操作关闭应用。

### Style

UI and UX design follow modern minimalist principles, prioritizing usability and clarity. The interface is clean and intuitive, with a focus on essential features to streamline the user experience.

## Tech Stack

- **Runtime**: Electron.js
- **Language**: TypeScript
- **Frontend Framework**: React
- **UI Library**: shadcn/ui + Radix UI
- **Styling**: Tailwind CSS
- **Virtualization**: react-window
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
