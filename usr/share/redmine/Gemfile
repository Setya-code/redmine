source 'https://rubygems.org'

gem "bundler", ">= 1.5.0"

gem "rails", "~> 5.2.2"
gem "rouge", "~> 3.7"
gem "request_store", "~> 1.1"
gem "mini_mime", "~> 1.0"
gem "actionpack-xml_parser"
gem "roadie-rails", "~> 1.3"
gem "mimemagic"
gem "mail", "~> 2.7"
gem "csv", "~> 3.0" if RUBY_VERSION >= "2.3" && RUBY_VERSION < "2.6"

gem "nokogiri", (RUBY_VERSION >= "2.3" ? "~> 1.10" : "~> 1.9")
gem "i18n", "~> 1.5"
gem "xpath", "< 3.2.0" if RUBY_VERSION < "2.3"

# TODO: Remove the following line when #32223 is fixed
gem "sprockets", "~> 3.7.2"

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :x64_mingw, :mswin]
gem "rbpdf", "~> 1.19"

# Optional gem for LDAP authentication
group :ldap do
  gem "net-ldap", "~> 0.16"
end

platforms :mri, :mingw, :x64_mingw do
  # Optional gem for exporting the gantt to a PNG file, not supported with jruby
  group :rmagick do
    gem "rmagick", "~> 2.16"
  end

  # Optional Markdown support, not for JRuby
  group :markdown do
    gem "redcarpet", "~> 3.4"
  end
end

# Include database gems for the adapters found in the database
# configuration file
require 'erb'
require 'yaml'

seen_adapters = {}
Dir['/usr/share/redmine/{config,instances/*/config}/database.yml'].select do |f|
  File.readable?(f)
end.each do |database_file|
  database_config = YAML::load(ERB.new(IO.read(database_file)).result)
  adapters = database_config.values.map {|c| c['adapter']}.compact.uniq
  if adapters.any?
    adapters.each do |adapter|
      next if seen_adapters[adapter]
      seen_adapters[adapter] = true
      case adapter
      when 'mysql2'
        gem "mysql2", "~> 0.5", :platforms => [:mri, :mingw, :x64_mingw]
      when /postgresql/
        gem "pg", "~> 1.1", :platforms => [:mri, :mingw, :x64_mingw]
      when /sqlite3/
        gem "sqlite3", "~>1.3", :platforms => [:mri, :mingw, :x64_mingw]
      when /sqlserver/
        gem "tiny_tds", "~> 1.0", :platforms => [:mri, :mingw, :x64_mingw]
        gem "activerecord-sqlserver-adapter", :platforms => [:mri, :mingw, :x64_mingw]
      else
        warn("Unknown database adapter `#{adapter}` found in config/database.yml, use Gemfile.local to load your own database gems")
      end
    end
  else
    warn("No adapter found in config/database.yml, please configure it first")
  end
end

# Load plugins' Gemfiles
Dir.glob File.expand_path("../plugins/*/{Gemfile,PluginGemfile}", __FILE__) do |file|
  eval_gemfile file
end
