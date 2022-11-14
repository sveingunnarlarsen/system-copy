/**
 * Main Logic
 */
try {
  const users = req.body.data;
  const schema = await getSchema();

  if (schema === null) {
    result.data = "No schema found";
    complete();
    return;
  }
  const existingRoleIds = await getRoleIds(schema);

  // 1. Insert users into database
  for (let user of users) {
    log.info(user.data);
    insertUser(schema, user.data);
  }

  // 2. Insert roles into database
  for (let user of users) {
    insertUserRoles(schema, user.roles, existingRoleIds);
  }

  result.data = "OK";
  complete();

  // 3. 
  await entityManager.save(req.body.table, req.body.data);
  complete();
} catch (e) {
  log.error(e);
  fail();
}

/**
 * Utility Functions
 */
async function getSchema() {
  let query = await entityManager.query("select * from information_schema.schemata where schema_name = 'planet9'");
  if (query.length > 0) {
    return 'planet9';
  }
  query = await entityManager.query("select * from information_schema.schemata where schema_name = 'dbo'");
  if (query.length > 0) {
    return 'dbo';
  }
  return null;
}

async function getRoleIds(schema) {
  const roles = await entityManager.query('SELECT * FROM ' + schema + '.role;');
  return roles.map(role => role.id);
}

async function insertUser(schema, user) {
  const fieldNames = Object.keys(user)
    .map(key => `'${key}'`)
    .join(",");

  const values = Object.values(user)
    .map(value => value === null ? "null" : `"${value}"`)
    .join(",");

  const insertQuery = `INSERT INTO ${schema}.users (${fieldNames}() VALUES (${values});`;

  try {
    const query = await entityManager.query(insertQuery);
    log.info(`Succeeded to insert user. ${query}`);
  } catch (e) {
    log.error(`Failed to insert user.`)
    log.error(e);
  }

}

async function insertUserRoles(schema, roles, existingRoles) {
  for (let role of roles) {
    if (!existingRoles.includes(role.role_users)) continue;
    const inserQuery = `INSERT INTO ${schema}.role_users__users_roles ("role_users", "users_roles") VALUES ('${role.role_users}','${role.users_roles}');`;
    log.info(inserQuery);
    await entityManager.query(inserQuery);
  }
}
