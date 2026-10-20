# HeatShield hourly deploy

This repository only runs a scheduled job. It holds no project code.

Every hour (minute 17) the job in `.github/workflows/hourly-deploy.yml`:

1. takes the latest code and trusted data from the team repository [laiba2526/heatshield-pakistan](https://github.com/laiba2526/heatshield-pakistan) (read-only),
2. downloads fresh Open-Meteo data and runs Quality Gate 1, the Data Quality Agent and Quality Gate 2 (`python run.py update`),
3. only if both gates pass, publishes the new trusted data to the Hugging Face Space [NeuralPowerX/heatshield-pakistan](https://huggingface.co/spaces/NeuralPowerX/heatshield-pakistan).

If a gate fails, nothing is published and the Space keeps showing the last trusted data. Nothing is ever written back to the team repository.

## One-time setup

In this repository: **Settings → Secrets and variables → Actions → New repository secret**

| Name | Value |
| --- | --- |
| `HF_TOKEN` | A Hugging Face **write** token for the `NeuralPowerX/heatshield-pakistan` Space |

Optional repository variable `HF_SPACE` changes the target Space (default `NeuralPowerX/heatshield-pakistan`).

Then **Actions → hourly-deploy → Run workflow** to test it once.

## Notes

- Each run starts from the data committed in the team repository and adds the last 14 days from Open-Meteo, so the Space always shows the newest trusted forecast.
- GitHub pauses scheduled jobs in a repository with no activity for 60 days; one click on **Run workflow** (or any commit) starts them again.
