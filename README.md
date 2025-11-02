# AIOps-Log-Anomaly-Detection

项目：日志异常检测与告警聚合（AIOps 入门）

概述：
- 将非结构化日志解析为结构化字段，基于窗口级特征与文本特征进行无监督异常检测（IsolationForest）。
- 实现告警聚合（基于 TF-IDF + cosine 相似度或特征聚类）。
- 提供简单的模拟器、训练脚本、推理 pipeline 与 Docker 化模型服务。

快速开始（本地）：
1. 克隆仓库并进入目录
2. 创建虚拟环境并安装依赖：
   - python -m venv .venv && source .venv/bin/activate
   - pip install -r requirements.txt
3. 生成模拟日志并解析：
   - python scripts/simulate_logs.py --out data/simulate_logs.log --lines 20000
   - python src/log_parser.py --input data/simulate_logs.log --out data/parsed_logs.parquet
4. 提取特征并训练：
   - python src/feature_extraction.py --input data/parsed_logs.parquet --out data/features.parquet
   - python src/train_isolation_forest.py --input data/features.parquet --model out/model_isoforest.pkl
5. 运行 pipeline（对新日志进行预测）：
   - python src/pipeline.py --input data/simulate_logs.log --model out/model_isoforest.pkl --out results/alerts.csv

可选：
- docker build -t aioops-log-detector .
- docker run --rm -p 8000:8000 aioops-log-detector

内容结构：
- scripts/: 工具脚本（日志模拟器）
- src/: 核心代码（解析、特征、训练、pipeline、服务）
- data/: 解析与特征文件（gitignore 大文件）
- out/: 模型与结果输出
- notebooks/: EDA 与实验 notebook

结果与评估：
- results/alerts.csv 包含 anomaly_score、window_start、cluster_id、example_messages
- 如有标签，可计算 Precision/Recall 或 ROC-AUC

联系与扩展：
- 下一步建议：接入 Prometheus/ELK、使用 Autoencoder 对比、实现实时流式处理（Kafka/Flink）。
