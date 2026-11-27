Status: brainstorm
Topics: openpractice-admin-delta-marley
Date: 2026-11-27

# Delta A6 — Uniform API Surface

## What the capability is

A consistent, automatically generated API across every entity: REST
cRUD on all DocTypes, RPC for server methods, token/session auth,
role-scoped permissions enforced at the API layer, plus webhooks for
outbound events.

## How the leader implements it

Frappe generatesthe API from DocType metadata — every new entity,
including customer-specific customizations created via A2, is
API-addressable with the same auth and permission model. No per-entity
endpoint authoring.

## Where the other system stands

OpenEMR has a growing REST API and a strong FHIR implementation for
clinical data, but administrative entities (billing internals,
inventory, facilities) have thinner, less uniform coverage; some
surfaces are only reachable via its form-level UI or direct DB access.

## Implication for openPractice

This is the integration and automation story: connectors, reporting
pipelines, and agent-driven workflows can treat the whole product as
API-addressable. It also raises the bar for upstream choices — any
custom module that bypasses DocTypes breaks the uniformity, so the
A2 convention doubles as an architecture constraint. For your own
toolchain (agents, CI against a live instance), the uniform surface is
directly usable today.

## Build / adopt options

- Adopt; commit to "no entity outside DocTypes" as an openPractice
  architecture rule.
- Publish an API catalogue early (generated docs) as an implementer asset.

## Verification links

- Frappe REST API: https://frappeframework.com/docs/user/en/api
