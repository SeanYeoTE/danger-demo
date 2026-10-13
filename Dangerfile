# ---------- Config ----------
MIN_DESCRIPTION_LENGTH = 30
TITLE_PATTERN = /\A(feat|fix|docs|refactor|test|chore|ci|perf)(\([\w\-]+\))?: .{3,}/
SIZE_WARN_LINES = 400
SIZE_FAIL_LINES = 1000
SOURCE_PATTERN = %r{\ATodoList/.*\.swift\z}
DOC_PATTERN = %r{(\AREADME\.md\z|\Adocs/|\.md\z)}

# ---------- Check 1: PR Description ----------
body = github.pr_body.to_s.strip
if body.length < MIN_DESCRIPTION_LENGTH
  fail("**PR description is too short.** Describe what changed and why (min #{MIN_DESCRIPTION_LENGTH} characters).")
end

# ---------- Check 2: PR Title ----------
unless github.pr_title =~ TITLE_PATTERN
  fail("**PR title must follow `type(scope): summary`**, e.g. `feat(todo): add due dates`. Allowed types: feat, fix, docs, refactor, test, chore, ci, perf.")
end

# ---------- Check 3: PR Labels ----------
if github.pr_labels.empty?
  fail("**Add at least one label** to this PR (e.g. `bug`, `enhancement`, `documentation`).")
end

# ---------- Check 4: PR Size ----------
changed_lines = git.lines_of_code
if changed_lines > SIZE_FAIL_LINES
  fail("**PR is too large (#{changed_lines} lines changed).** Split it into smaller PRs (limit: #{SIZE_FAIL_LINES}).")
elsif changed_lines > SIZE_WARN_LINES
  warn("**Large PR (#{changed_lines} lines changed).** Consider splitting it (recommended: under #{SIZE_WARN_LINES}).")
end

# ---------- Check 5: Documentation ----------
changed_files = git.added_files + git.modified_files + git.deleted_files
source_changed = changed_files.any? { |f| f =~ SOURCE_PATTERN }
docs_changed = changed_files.any? { |f| f =~ DOC_PATTERN }

if source_changed && !docs_changed
  warn("**Source code changed but no documentation was updated.** Update the README or docs if behavior changed.")
end

# ---------- Confirmation ----------
if status_report[:errors].empty? && status_report[:warnings].empty?
  message("✅ All PR checks passed.")
end