---
title: Workarea 3.3.30
excerpt: Patch release notes for Workarea 3.3.30.
---

# Workarea 3.3.30

Patch release notes for Workarea 3.3.30.

## Fix Delayed Loading of Chrome Options Configuration

`Capybara.register_driver` does not run the passed block immediately, which
can cause issues with the aliasing of `Workarea.config.headless_chrome_options`,
particularly with multi-site where the settings are duplicated. Options
are now loaded prior to being passed into the block, ensuring that they
are the same for each site.

### Issues

- [ECOMMERCE-6969](https://jira.tools.weblinc.com/browse/ECOMMERCE-6969)

### Pull Requests

- [4108](https://stash.tools.weblinc.com/projects/WL/repos/workarea/pull-requests/4108/overview)


## Fix Sidekiq Auto-Configuration

Allow configuring pool timeout on the Sidekiq client (the part of
Sidekiq that runs within the Rails application server). This also
removes automatically scaling the number of concurrent jobs that
can be run simultaneously by how many processes of Sidekiq are running
at once. This caused some hard-to-debug issues, and is handled in better
ways within the OS you're hosting Workarea on. This change also allows
configuration of the PID file path and queues using the environment
variables `$WORKAREA_SIDEKIQ_PIDFILE` and `$WORKAREA_SIDEKIQ_QUEUES`.

### Issues

- [ECOMMERCE-6967](https://jira.tools.weblinc.com/browse/ECOMMERCE-6967)

### Pull Requests

- [4105](https://stash.tools.weblinc.com/projects/WL/repos/workarea/pull-requests/4105/overview)


## Update Puma and Loosen Constraint

This gem is fairly stable and doesn't follow strict semantic versioning
anyways. Update to the latest Puma to enjoy its much improved reactor
internals that will help with concurrency on high traffic deployments.

### Issues

- [ECOMMERCE-6984](https://jira.tools.weblinc.com/browse/ECOMMERCE-6984)

### Pull Requests

- [4113](https://stash.tools.weblinc.com/projects/WL/repos/workarea/pull-requests/4113/overview)

## Fix Encoding Errors on CSV Imports

Allow users to specify a source encoding for CSV files that are being
imported into the application. UTF-8 encoding is still enforced, since
that's the charset Workarea renders content with in the browser, but
the source can now be configured to prevent errors when importing CSV.
Any other options passed into `CSV.foreach` can be configured using the
`Workarea.config.csv_import_options` Hash.

See the documentation on `CSV.foreach` for more information: https://ruby-doc.org/stdlib-2.6.3/libdoc/csv/rdoc/CSV.html#method-c-foreach

### Issues

- [ECOMMERCE-6963](https://jira.tools.weblinc.com/browse/ECOMMERCE-6963)

### Pull Requests

- [4100](https://stash.tools.weblinc.com/projects/WL/repos/workarea/pull-requests/4100/overview)


## Fix Search Settings Clearing When Not Submitted From Main Form

With the **workarea-swatches** plugin comes a form that updates a single
value on the search settings, in contrast to the main search settings
form which updates all values at once. This form's action URL is just
`PATCH /admin/search_settings`, which does some massaging of the params
and inadvertently causes `nil` values to be contained in the attributes
hash for updating when those params are not included in the request.
Workarea now runs `.compact` on the Hash of attributes generated by this
controller action, before it's mass-assigned into `Search::Settings.current`.
This prevents the params from getting cleared out, and fixes any kind of partial
updates on the `/admin/search_settings` endpoint.

### Issues

- [ECOMMERCE-6942](https://jira.tools.weblinc.com/browse/ECOMMERCE-6942)

### Pull Requests

- [4094](https://stash.tools.weblinc.com/projects/WL/repos/workarea/pull-requests/4094/overview)
- [4073](https://stash.tools.weblinc.com/projects/WL/repos/workarea/pull-requests/4073/overview)

