has_app_changes = !git.modified_files.grep(/app/).empty?
has_spec_changes = !git.modified_files.grep(/spec/).empty?
modified_and_added_files = git.modified_files + git.added_files

# Sometimes it's a README fix, or something like that - which isn't relevant for
# including in a project's CHANGELOG for example
# declared_trivial = github.pr_title.include? "#trivial"

# To run locally run command:
#   DANGER_GITHUB_API_TOKEN="YOUR_API_TOKEN" danger local

# Make it more obvious that a PR is a work in progress and shouldn't be merged yet
warn("PR is classed as Work in Progress") if github.pr_title.include? "[WIP]"

# Warn when there is a big PR
# warn("Big PR ") if git.lines_of_code > 500

# Don't let testing shortcuts get into master by accident
fail("fdescribe left in tests") if `grep -r fdescribe specs/ `.length > 1
fail("fit left in tests") if `grep -r fit specs/ `.length > 1

# --------------------------------------------------------------------------------------------------------------------
# You've made changes to lib, but didn't write any tests?
# --------------------------------------------------------------------------------------------------------------------
if has_app_changes && !has_spec_changes
  warn("There're app changes, but not tests. That's OK as long as you're refactoring existing code.", sticky: false)
end

# --------------------------------------------------------------------------------------------------------------------
# You've made changes to specs, but no library code has changed?
# --------------------------------------------------------------------------------------------------------------------
if !has_app_changes && has_spec_changes
  message('We really appreciate pull requests that demonstrate issues, even without a fix. That said, the next step is to try and fix the failing tests!', sticky: false)
end

# --------------------------------------------------------------------------------------------------------------------
# Don't let testing shortcuts get into master by accident,
# ensuring that we don't get green builds based on a subset of tests.
# --------------------------------------------------------------------------------------------------------------------

(modified_and_added_files - %w(Dangerfile)).each do |file|
  next unless File.file?(file)
  contents = File.read(file)

  if file.start_with?('spec')
    fail("`xit` or `fit` left in tests (#{file})") if contents =~ /^\w*[xf]it/
    fail("`fdescribe` left in tests (#{file})") if contents =~ /^\w*fdescribe/
  end

  # --------------------------------------------------------------------------------------------------------------------
  # If forgotten debuggers
  # --------------------------------------------------------------------------------------------------------------------
  if file.in?(%w(Gemfile Gemfile.lock)) (contents.include?('byebug') || contents.include?('binding.pry'))
    fail("Debugger forgotten in code!")
  end

  # --------------------------------------------------------------------------------------------------------------------
  # If forgotten puts
  # --------------------------------------------------------------------------------------------------------------------
  # if contents.include?('puts ') || contents.include?('p ') || contents.include?('ap ')
    # warn("Console puts forgotten in source code.")
  # end

  # --------------------------------------------------------------------------------------------------------------------
  # If 'if true' or 'if false'
  # --------------------------------------------------------------------------------------------------------------------
  if contents.include?('if true') || contents.include?('if false')
    fail("Invalid condition in source code 'if true' or 'if false'.")
  end

  # --------------------------------------------------------------------------------------------------------------------
  # If somebody commits Slim template
  # --------------------------------------------------------------------------------------------------------------------

  if file.include?('.slim')
    warn("No, we will really not use Slim templating engine.")
  end
end

# --------------------------------------------------------------------------------------------------------------------
# If somebody commits .DS_Store file
# --------------------------------------------------------------------------------------------------------------------

if modified_and_added_files.include?(".DS_Store")
  fail(".DS_Store commited! Remove it!")
end

# --------------------------------------------------------------------------------------------------------------------
# Rubocop
# --------------------------------------------------------------------------------------------------------------------

if defined? rubocop
  # TODO: comment PR on line where it happened
  # github.api.create_pull_request_comment()
  rubocop.lint
else
  warn("Rubocop plugin not set. Add `gem 'danger-rubocop'` to your gemfile.")
end

# --------------------------------------------------------------------------------------------------------------------
# Simplecov (coverage)
# --------------------------------------------------------------------------------------------------------------------

if defined? simplecov
  simplecov.report('coverage/coverage.json')
else
  warn("Simplecov plugin not set. Add `gem 'danger-simplecov_json'` to your gemfile.")
end

# --------------------------------------------------------------------------------------------------------------------
# JUnit (tests result)
# --------------------------------------------------------------------------------------------------------------------

if defined? junit
  # junit.parse 'rspec.xml'
  # junit.headers = [:name, :file]
  # junit.report
else
  warn("JUnit plugin not set. Add `gem 'danger-junit'` to your gemfile.")
end
