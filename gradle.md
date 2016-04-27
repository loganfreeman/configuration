processResources
---
```groovy
def getTodayDate() {
  def today = new Date()
  return String.valueOf(today.getTime())
}

def getCommitRevision() {
  def revCmd = "git rev-parse --short HEAD"
  def revision = revCmd.execute()
  return revision.text.trim()
}

def getBranch() {
  def branchCmd = "git symbolic-ref -q --short HEAD";
  def branch = branchCmd.execute().text.trim();

  if (branch.length() == 0) {
    branchCmd = "git describe --tags --exact-match";
    branch = branchCmd.execute().text.trim();
  }
  return branch;
}

processResources {
  from(sourceSets.main.resources.srcDirs) {
    include 'application.properties'

    def datasource_dialect = (database_type == "mysql") ? datasource_mysql_dialect : datasource_oracle_dialect
    filter(
      ReplaceTokens,
      tokens: [
        datasource_jndi: datasource_jndi,
        datasource_type: deployment_type,
        datasource_dialect: datasource_dialect,
        database_type: database_type,
        app_data_dir: props.app_data_dir,
        plugin_dir: props.plugin_dir,
        show_sql: show_sql,
        buildinfo_version : getBranch(),
        buildinfo_date : getTodayDate(),
        buildinfo_commit_revision : getCommitRevision()])
  }
}
```
