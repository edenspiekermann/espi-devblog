---
layout: post
author: niall-burkley
title: "Creating a multi-tenant site with rails"
---

We recently worked on a joint-venture project between two companies. The goal was to build one "white-label" site that would be released in two different markets. Apart from some branding, the sites would be identical, but completely separate.

We had a few options:

* build one site, creating a clone for the second site
* build one reusable site framework and host two different versions with changes on top
* build a multi-tenant site that will manage both versions at once

We chose the third option as it would result in building, hosting and maintaining just a single application.

## Multi-tenancy with rails

Once we had decided we were going to build a multi-tenant site, we then needed to pick the most suitable approach. There's two main ways to build a multi-tenant ruby on rails application:

* scoping the data

This option requires scoping the data by the site it belongs to. In essence, adding a `site_id` to database tables and scope all database queries to only return data belonging to that site.

* scoping the database

This option creates separate tables within the database for each site. Database queries for a site only use database tables belonging to that site.

When it comes to databases at Edenspiekermann, we ❤️ PostgreSQL. It's got a very nice way of segregating a database using [schemas](https://www.postgresql.org/docs/9.5/static/ddl-schemas.html), we chose to go with the second option using PostgreSQL schemas.

## PostgreSQL schemas

Our two sites were going to be completely separate - there's no shared data, and data from one site should not be visible to the other.

By using schemas, we can create two separate versions of the site and restrict request from each site to only access their own database tables. We also remove the need for any site specific schema details like storing `site_id` on tables. As far as each site is concerned, the sub-section of the database it has access to, is the only database there is.

## Apartment Gem

Ryan Bates has a [great railscast](http://railscasts.com/episodes/389-multitenancy-with-postgresql) on how to create a multi-tenant site using PostgreSQL schemas and search paths. It's surprisingly quite straight forward.

We chose to use the [apartment gem](https://github.com/influitive/apartment) which does a great job of wrapping up and abstracting the logic. It looks after mapping the request coming in to the correct database schema or 'tenant' as it calls them. It also ensures that all database migrations are run or rolled back on all your schemas.

### Setup

Rather than going into detail on the setup instructions, there's an excellent screencast from Chris Oliver at [Go Rails](https://gorails.com) on [how setup a multi-tenant site with apartment](https://gorails.com/episodes/multitenancy-with-apartment). 

### Elevators

The apartment gem has the concept of 'Elevators' to switch between tenants. Elevator can be used to to switch the tenant based on the request coming in. Out of the box, requests can be matched by subdomain or host, but you can easily build your own Elevator to switch based on some custom attributes like headers or query params.

Since we have different sites on different domains, we match on hostname, using the `HostHash` elevator. However, by default, this elevator throws an exception if it doesn't find a match. For example, if a user requests `https://foo.somedomain.com` instead of `http://www.somedomain.com`. To handle this a bit better we created a custom Elevator that renders a static 404 page:

```ruby
class RescuedHostHash < ::Apartment::Elevators::HostHash

  def call(env)
    super
  rescue ::Apartment::TenantNotFound => e
    Rollbar.error(e, "ERROR: Apartment Tenant not found: #{Apartment::Tenant.current.inspect}")
    static_404_page = File.read(Rails.root.to_s + '/public/404.html')
    return [ 404,  {'Content-Type' => 'text/html' }, [static_404_page] ]
  end

end
```

### Site specific configuration

While both sites were very similar, we did need to be able to customize them both. To do this we stored some site-specific variables in a YAML file, wrapped those values in a simple and made it accessible to via a `current_site` helper method.

```yaml
# config/site-settings.yml
somedomain:
  name:     'Some Domain'
  email:    'info@somedomain.de'
  locale:   'de'
  timezone: 'Europe/Berlin'
otherdomain:
  name:     'Other Doman'
  email:    'info@otherdomain.co.uk'
  locale:   'en'
  timezone: 'Europe/London'
```

We wrap the site settings with a simple `Site.rb` ruby class loaded it using an application controller concern.

```ruby
# app/controllers/concerns/multisite.rb
module Multisite
  extend ActiveSupport::Concern

  included do
    before_action :set_locale, :set_timezone
    helper_method :current_site
  end

  private

  def current_site
    @current_site ||= Site.current
  end

  def set_locale
    I18n.locale = current_site.locale
  end

  def set_timezone
    Time.zone = current_site.timezone
  end
end
```

This not only looks after site-specific variable, but it also sets the correct locale and timezone for the duration of the request.

## Testing the setup

Getting the tests setup to set the correct site based on hostname was a little tricky at first. The domain `lvh.me` resolves to `127.0.0.1` (which is what rspec/capybara tests generally use. However, we can use subdomains with `lvh.me`. So to test out the multi-tenant logic, we created test-specific host mappings.

* `somesite.lvh.me` mapping to `somesite`
* `othersite.lvh.me` mapping to `othersite`

To setup Capybara to use `lvh.me` instead of `localhost` we added the following to our `rails_helper.rb`

```ruby
# set up tests to play nice with subdmomains (for testing multisite)
Capybara.always_include_port = true
Capybara.app_host = 'http://lvh.me'

# allow use of lvh.me as localhost for tests
Capybara::Webkit.configure do |config|
  config.allow_url('lvh.me')
end
```

Then we tested our multi-tenancy setup

```ruby
describe 'multisite setup' do
  context 'for somesite requests' do
    before do
      RSpec.configure do |config|
        Capybara.app_host = 'http://somesite.lvh.me'
      end
    end

    it 'should use the somesite schema for somesite requests' do
      visit root_path
      expect(Apartment::Tenant.current).to eq('somesite')
    end

    it 'should set the locale to de' do
      visit root_path
      expect(I18n.locale).to eq(:de)
    end
  end
end
```

## How it all went

Overall we were extremely happy with the finished product - and more importantly, our customers are too. We have two separate sites running off he same web application, sharing the entire stack, nearly cutting the costs and maintenance in half.

It took a bit of work ensuring everything was using the split up correctly such as namespacing the cache store, running the background jobs in the correct context, but it was well worth it.

The database is free from any multi-tenancy logic, we're saved any joins by  `site_id` improving performance and the data cannot leak from one schema to another.

Have any questions on the setup? Just [ask us](https://github.com/edenspiekermann/ama)!
