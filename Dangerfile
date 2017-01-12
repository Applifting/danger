# Sometimes it's a README fix, or something like that - which isn't relevant for
# including in a project's CHANGELOG for example
declared_trivial = github.pr_title.include? "#trivial"

# Make it more obvious that a PR is a work in progress and shouldn't be merged yet
warn("PR is classed as Work in Progress") if github.pr_title.include? "[WIP]"

# Warn when there is a big PR
warn("Big PR ") if git.lines_of_code > 500

# Don't let testing shortcuts get into master by accident
fail("fdescribe left in tests") if `grep -r fdescribe specs/ `.length > 1
fail("fit left in tests") if `grep -r fit specs/ `.length > 1

if defined? rubocop
  rubocop.lint
else
  warn('Rubocop plugin not set')
end

if defined? simplecov
  simplecov.report('coverage/coverage.json')
else
  warn('Simplecov plugin not set')
end

if defined? junit
  junit.parse 'rspec.xml'
  junit.headers = [:name, :file]
  junit.report
else
  warn('JUnit plugin not set')
end
