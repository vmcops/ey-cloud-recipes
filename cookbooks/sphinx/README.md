# Sphinx

This cookbook will install [Sphinx](http://sphinxsearch.com/) and setup an environment to use Thinking Sphinx 3. It has not been tested with Thinking Sphinx 2, however, if you need a cookbook for TS2, you can use the [old cookbook](https://github.com/engineyard/ey-cloud-recipes/tree/f3840c87d4f7475fff89dd4770adb546d1f2adbc/cookbooks/sphinx).

## Setup

This cookbook will install Sphinx on a  utility instance that matches the name set in `attributes/default.rb`. If no utility instance can be found with that name, Sphinx will be setup on every application instance (this includes the application master or solo instances).

By default, the cookbook will look for a utility instance named `sphinx` and will install Sphinx 2.0.6 to run for all applications in the environment. It will also setup a cronjob to reindex every 15 minutes. These settings can be modified in the `attributes/default.rb` file.

# Usage

Uncomment the following line inside of `main/recipes/default.rb`:

```
include_recipe "sphinx"
```

Then upload and apply your cookbooks:

```
ey recipes upload
ey recipes apply
```

You will also need to add a deploy hook to restart Sphinx on deploy. Create a file called `deploy/after_symlink.rb` inside of of you application with the following contents:

```
on_app_servers_and_utilities do
  run "[[ -d #{shared_path}/sphinx ]] && ln -nfs #{shared_path}/sphinx #{current_path}/db/sphinx"
  run "cd #{current_path} && RAILS_ENV=#{environment} bundle exec rake ts:configure"
end
```