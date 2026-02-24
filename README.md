# Kidney CT Scan Classification (CNN + DVC)

این پروژه یک پایپ‌لاین کامل یادگیری عمیق برای **طبقه‌بندی تصاویر CT کلیه** است که با `TensorFlow/Keras` پیاده‌سازی شده و با `DVC` مدیریت مراحل و مصنوعات (artifacts) را انجام می‌دهد.

مدل پایه `VGG16` (وزن‌های ImageNet) است و مراحل کار شامل دریافت داده، آماده‌سازی مدل پایه، آموزش و ارزیابی می‌شود.

## Features

- پایپ‌لاین مرحله‌بندی‌شده آموزش با DVC
- مدیریت تنظیمات از طریق `config/config.yaml` و `params.yaml`
- معماری ماژولار در `src/cnnClassifier`
- لاگ‌گیری در فایل `logs/running_logs.log`
- پشتیبانی از ثبت متریک در MLflow/Dagshub (در کد موجود است)

## Project Structure

```text
.
|-- config/
|   `-- config.yaml
|-- src/cnnClassifier/
|   |-- components/
|   |-- config/
|   |-- constants/
|   |-- entity/
|   |-- pipeline/
|   `-- utils/
|-- artifacts/                 # خروجی مراحل DVC (داده، مدل‌ها، ...)
|-- dvc.yaml                   # تعریف stages
|-- dvc.lock                   # قفل نسخه stages و artifacts
|-- params.yaml                # هایپرپارامترها
|-- main.py                    # اجرای کامل 4 مرحله پشت سر هم
`-- requirements.txt
```

## Pipeline Stages

مراحل تعریف‌شده در `dvc.yaml`:

1. `data_ingestion`
   - دانلود دیتاست از Google Drive
   - استخراج در `artifacts/data_ingestion/`
2. `prepare_base_model`
   - ساخت VGG16 پایه
   - افزودن head نهایی طبقه‌بندی
3. `training`
   - ساخت data generator
   - آموزش و ذخیره مدل نهایی در `artifacts/training/model.h5`
4. `evaluation`
   - ارزیابی مدل
   - ذخیره متریک در `scores.json`

## Requirements

- Python 3.10 یا 3.11 (پیشنهادی)
- pip
- Git
- DVC

> برای GPU باید نسخه TensorFlow متناسب با CUDA/CuDNN محیط شما نصب باشد.

## Installation

```bash
git clone https://github.com/sosp30/first_ai_image_cancer.git
cd first_ai_image_cancer

python -m venv .venv
# Windows
.venv\Scripts\activate
# Linux/macOS
source .venv/bin/activate

pip install --upgrade pip
pip install -r requirements.txt
```

## Run The Project

### 1) اجرای کل پایپ‌لاین با Python

```bash
python main.py
```

### 2) اجرای کل پایپ‌لاین با DVC

```bash
dvc repro
```

### 3) اجرای مرحله‌ای

```bash
python src/cnnClassifier/pipeline/stage_01_data_ingestion.py
python src/cnnClassifier/pipeline/stage_02_prepare_base_model.py
python src/cnnClassifier/pipeline/stage_03_model_training.py
python src/cnnClassifier/pipeline/stage_04_model_evaluation.py
```

## Key Configuration

- `config/config.yaml`
  - مسیر artifactها
  - لینک دیتاست
  - مسیر مدل‌ها
- `params.yaml`
  - `IMAGE_SIZE`, `BATCH_SIZE`, `EPOCHS`
  - `LEARNING_RATE`, `AUGMENTATION`, ...

برای تغییر رفتار آموزش، فقط همین دو فایل را آپدیت کنید و سپس `dvc repro` بزنید.

## Metrics

متریک‌های ارزیابی در فایل `scores.json` ذخیره می‌شوند. خروجی فعلی ریپو:

```json
{
  "loss": 0.0,
  "accuracy": 1.0
}
```

## Logging

لاگ اجراها در مسیر زیر ذخیره می‌شود:

```text
logs/running_logs.log
```

## Notes

- در `main.py` مقادیر `MLFLOW_TRACKING_USERNAME` و `MLFLOW_TRACKING_PASSWORD` به‌صورت hard-coded قرار داده شده‌اند. برای استفاده در محیط واقعی، این مقادیر را به **Environment Variable** امن منتقل کنید.
- فایل `templates/index.html` در این نسخه خالی است؛ بنابراین تمرکز فعلی ریپو روی پایپ‌لاین آموزش/ارزیابی است.

## Contributing

1. یک branch جدید بسازید.
2. تغییرات را commit کنید.
3. Pull Request باز کنید.

## License

در حال حاضر فایل لایسنس در ریپو موجود نیست. در صورت نیاز، یک `LICENSE` مناسب (مثل MIT/Apache-2.0) اضافه شود.
