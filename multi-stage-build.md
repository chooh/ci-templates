# Multi-Stage Builds

Бывает, что надо собрать несколько версий контейнера, например development (с включённой отладкой
и зависимостями для разработки) и production. Для этого лучше всего подходят multi-stage builds. Но
с ними есть ряд проблем при использованиии кешей.

Поэтому сначала собирается базовый стейдж. Затем собираются все остальные стейджи с кешом от базового
и себя самого.

Применяется следующая схема именования тегов: `{branch}-{stage}-{pipeline_id}`.

## Example:

`Dockerfile`:

```
FROM php:7.2-fpm AS base
...

FROM base AS prod
...

FROM base AS dev
...
```

`.gitlab-ci.yml`:

```
include:
  - project: 'demo/ci-templates'
    file: '/multi-stage-build.yml'

stages:
  - prebuild
  - build

base:
  stage: prebuild
  extends: .base_build

prod:
  stage: build
  extends: .stage_build

dev:
  stage: build
  extends: .stage_build
```
