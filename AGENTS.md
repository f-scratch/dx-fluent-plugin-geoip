# AGENTS.md

このリポジトリで AI コーディングエージェントが作業するときの指示。

## リポジトリの役割

- [y-ken/fluent-plugin-geoip](https://github.com/y-ken/fluent-plugin-geoip) を fork し、同梱の GeoIP データベース（MaxMind GeoLite2-City）を更新して使うための Fluentd フィルタプラグイン gem（`dx-fluent-plugin-geoip`）。
- 直近の変更はほぼ `data/GeoLite2-City.mmdb` の差し替えと gemspec のバージョン更新（DEV021297 / DEV021526 / DEV021797）。

## 技術スタック

- Ruby gem。runtime 依存は fluentd（`>= 0.14.8, < 2`）、maxminddb、geoip2_c、dig_rb（`dx-fluent-plugin-geoip.gemspec`）。
- テストは test-unit + test-unit-rr。appraisal で fluentd v1.0 の組み合わせを定義（`Appraisals` / `gemfiles/`）。

## ディレクトリ構成

- `lib/fluent/plugin/filter_geoip.rb` — プラグイン本体。`geoip2_database` の既定値は `data/GeoLite2-City.mmdb`。
- `data/` — GeoIP データベース（`GeoLite2-City.mmdb`、`GeoLiteCity.dat`）とライセンス表記。
- `test/plugin/test_filter_geoip.rb` — テスト。
- `utils/dump.rb` — 指定 IP の GeoIP 検索結果を出力する確認用スクリプト。

## 開発コマンド

- テスト: `bundle exec rake test`（Rakefile の default タスク）。
- `.travis.yml` / `docker-compose.yml` / `dockerfiles/`（Ruby 2.1〜2.5）は fork 元由来。GitHub Actions は PR をプロジェクトボードに追加するだけで、テストを実行する CI は無い。

## 変更時のルール

- mmdb を差し替えるときは `dx-fluent-plugin-geoip.gemspec` の `spec.version` も上げる（直近は 4.2.1 → 4.3.0 → 5.0.2）。
- gemspec の `spec.files` は `git ls-files` なので、gem に含めたいファイルはコミットされている必要がある。

## ブランチと PR

- default ブランチは `develop`。作業ブランチ `<種別>/<課題ID>_<概要>`（例: `feature/DEV0xxxxx_update_geoip_version`）から `develop` へ PR を出す。`develop_YYYYMMDD` 運用は無い。
- PR タイトルは `[課題ID] 概要`。本文は組織共通テンプレート（https://github.com/f-scratch/.github/blob/master/.github/PULL_REQUEST_TEMPLATE.md）に従う。
- 実装ルール: https://github.com/f-scratch/dx-windsurf-rules の `rules/ruby_rules.md`、`rules/comment_rules.md`。

## してはいけないこと

- `data/` のライセンス表記（`COPYRIGHT.txt` / `LICENSE.txt`）を削除しない。
