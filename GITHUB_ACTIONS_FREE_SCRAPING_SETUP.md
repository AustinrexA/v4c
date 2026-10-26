# Free daily V4Career scraping with GitHub Actions

This workflow is designed for the repository layout in the supplied ZIP, where the
Flask project is in the `V4Career/` subfolder. It does not edit Render settings,
deploy the website, run database migrations, or use Apify. It runs the project's
existing official-company structured collector and writes results to the Neon
PostgreSQL database configured through GitHub Actions secrets.

## Schedule

- Daily at 07:00 India Standard Time (01:30 UTC).
- You can also start a test run from **GitHub → Actions → V4Career Daily Free Company Scrape → Run workflow**.
- GitHub scheduled runs can start late during busy periods. GitHub Actions is free only within the account/repository's included usage allowance.

## Add the workflow to GitHub

1. Extract this ZIP locally.
2. In your GitHub repository, check whether `V4Career/` is a subfolder at the repository root.
3. If it is, copy `.github/workflows/v4career-daily-scrape.yml` from this ZIP to the repository-root `.github/workflows/` directory. Keep its `working-directory: ./V4Career` and `V4Career/requirements.txt` paths.
4. If the repository root is already the contents of the `V4Career/` project folder (there is no `V4Career/` subfolder in GitHub), do **not** use the workflow as-is. Change `working-directory` to `.` and `cache-dependency-path` to `requirements.txt`, then remove the `V4Career/` prefix from the cache path. Put the workflow in the repository-root `.github/workflows/` directory.
5. Commit and push the workflow file to the repository's default branch.

## Configure GitHub secrets

Open **Repository → Settings → Secrets and variables → Actions → New repository secret** and add these secrets using the values for the *same Neon database used by the live Render website*:

- `NEON_HOST`
- `NEON_PORT` (usually `5432`)
- `NEON_DB`
- `NEON_USER`
- `NEON_PASSWORD`

Do not paste credentials into the workflow, commit them to Git, or share them in chat. The workflow sets `NEON_SSLMODE=require` and `DB_ENGINE=postgres`.

## First-run safety

1. Run the workflow manually once and inspect its logs.
2. Confirm that job-run records and jobs are written to the intended Neon database and appear in the live portal.
3. The collector only accepts structured `JobPosting` data from configured official company hosts and requires an India location. It may legitimately collect zero jobs for sites that don't expose this data; some sites may block automated requests.
4. A source failure causes the batch to finish its other sources and then mark the workflow failed, so review the log before assuming all companies were scraped.
5. This workflow intentionally does not run the Apify portal batch, so it avoids spending Apify credits. Direct requests are used only by the existing structured company collector.
6. It does not run SQL migrations or change the Render deployment. However, it **does write job/source/run data to Neon**, so validate the secrets carefully before the first run.

## Free usage note

GitHub Actions usage is subject to the free allowance and account settings. The workflow has a 30-minute job timeout, a small batch size, and a two-worker limit to reduce usage. Check **GitHub → Settings → Billing and licensing → Usage** for current allowance.
