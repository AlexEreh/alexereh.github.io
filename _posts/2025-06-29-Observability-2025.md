---
title: Современный Observability
date: 2025-06-29 19:00:00 +0300
categories: [Kubernetes, Observability]
tags: [kubernetes, observability, ebpf, coroot, kibana, grafana, prometheus, elasticsearch, logstash, fluentd, fluent-bit, jaeger, opentelemetry, tracing]
description: Главное различие между вещью, которая может сломаться, и той, которая не может, состоит в том, что, когда вещь, которая может сломаться, все-таки сломается, до нее невозможно будет добраться и починить. ©️ Дуглас Адамс. "В основном безвредна" (1992)
---

## Основные понятия Observability

### Метрики

Метрики - это числовые значения, которые описывают состояние системы. Они могут быть использованы для мониторинга и управления приложениями.

### Логи

Логи - это структурированные текстовые сообщения, которые содержат информацию о происходящих в работе системы событиях разной степени важности. Они могут быть использованы для отслеживания ошибок и проблем.

### Трассировка

Трассировка - это технология, которая позволяет отслеживать запросы (прежде всего между несколькими сервисами) и их обработку в системе. Она может быть использована для того чтобы выявлять проблемы в межсервисных взаимодействиях. Представьте: один запрос подразумевает вызов нескольких других сервисов. А теперь представьте что будет если один из сервисов не отвечает или отвечает слишком долго, или отвечает с ошибкой. Трассировка может помочь вам найти проблему и решить ее.

### Алерты

Алерты - это уведомления о проблемах в системе. Они могут быть использованы для мониторинга и управления приложениями. Представьте: вы на ***продуктовом*** проекте, где между пользователями системы и SRE (либо в худшем случае разработчиками) есть несколько должностей сотрудников, и пока проблема дойдёт до того, кто должен её устранять пользователи злостно начнут удалять аккаунты или **отказываться от использования системы**. Действительно ужасная ситуация. 

Алерты решают эту проблему путём уведомления через срочные каналы связи дежурных сотрудников, которые могут быстро реагировать на проблемы и устранять их.

В совокупности с продуманным инцидент-менеджментом алертинг позволяет быстро реагировать на "нездоровое" поведение системы.

### Мониторинг
Мониторинг - это процесс наблюдения за состоянием системы и ее компонентов. Он может быть использован для мониторинга и управления приложениями.

## Стек Observability ранее

### Kibana

Kibana - визуализация логов приложений, хранящихся в Elasticsearch и позволяющая делать по ним сложную выборку.

![kibana](/assets/media/kibana.webp)
_Интерфейс поиска логов Kibana_

### Prometheus

Prometheus - это система сбора и хранения метрик, которая позволяет хранить и делать выборку хранимых метрик в системе, а также отправлять алерты. Всего типов метрик: счетчики (counter, gauge), гистограммы (histogram), и суммы (summary).

### AlertManager

AlertManager - компонент Prometheus, который позволяет отправлять уведомления о проблемах в системе в произвольные источники (например, Slack, Telegram, Email, и т.д.), предварительно их группируя и фильтруя.

### Grafana

Grafana - это плаформа для визуализации данных, которая позволяет создавать дашборды и визуализировать метрики, логи и трассировки. Она поддерживает множество источников данных, таких как Prometheus, Elasticsearch, InfluxDB и другие.

![grafana](/assets/media/grafana.png)
_Пример дашборда Grafana_

### Jaeger

Jaeger - это система распределённой трассировки, помогающая диагностировать проблемы в распределённых системах. Она предоставляет инструменты для отслеживания запроса по мере его прохождения через систему. Ключом к этому является проброс идентификатора трейса (Trace ID) в заголовках запросов и иных асинхронных каналов коммуникации. 

![jaeger](/assets/media/jaeger.png)
_Интерфейс Jaeger_

## Новинки в Observability

### coroot

coroot - это набор инструментов для мониторинга и управления приложениями. В целом он предоставляет те же функции, что и Prometheus + Grafana, но с более широким набором инструментов и возможностей.

Особенно стоит отметить, что coroot предоставляет инструментарий для непрерывного профилирования приложений, используя технологию eBPF. Теперь не обязательно как по старинке выполнять команды pprof для получения профилей CPU/RAM, предварительно прокинув порты, достаточно просто зайти в coroot и найти приложение - там же и будет отображен flamegraph по времени выполнения определённых функций.

![coroot-flamegraph](/assets/media/coroot-flamegraph.png)
_Пример профиля сервиса без дополнительной инструментации_

Также coroot предоставляет сервисную карту того кластера, в котором он запущен. Это позволяет быстро и удобно находить и устранять проблемы с приложениями, без необходимости анализировать проблемы по всей цепочке вызовов.

![coroot-servicemap](/assets/media/coroot-servicemap.png)
_Пример сервисной карты кластера_

### Cilium (+ Hubble UI)

Cilium - это сетевой плагин для Kubernetes, который обеспечивает сетевую безопасность и мониторинг трафика в кластере. Он использует eBPF для обеспечения высокой производительности и минимизации влияния на производительность приложений.

Аналогично coroot в поставку Cilium входит компонент Hubble UI, который позволяет визуализировать и анализировать сетевые трафик и связи между сервисами в кластере.

![hubble-ui](/assets/media/hubble.png)
_Пример интерфейса Hubble UI_
