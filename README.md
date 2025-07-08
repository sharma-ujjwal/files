```
public List<RejectedUser> findByValueJdbc(String srcUserId, Long extrctSysId,
                                          String selectedDateCriteria, Date createdDateCriteria) {

    StringBuilder sql = new StringBuilder();
    List<Object> params = new ArrayList<>();

    sql.append("SELECT * FROM RejectedUser r ");
    sql.append("WHERE r.deleteFlag <> 'Y' ");
    sql.append("AND r.extractSystem <> 9 ");

    if (srcUserId != null && !srcUserId.isEmpty()) {
        sql.append("AND r.srcUserId LIKE ? ");
        params.add("%" + srcUserId + "%");
    }

    if (extrctSysId != null) {
        sql.append("AND r.extractSystem = ? ");
        params.add(extrctSysId);
    }

    if (createdDateCriteria != null) {
        if ("gt".equals(selectedDateCriteria)) {
            sql.append("AND trunc(r.createdDate) > ? ");
        } else if ("lt".equals(selectedDateCriteria)) {
            sql.append("AND trunc(r.createdDate) < ? ");
        } else if ("eq".equals(selectedDateCriteria)) {
            sql.append("AND trunc(r.createdDate) = ? ");
        }
        params.add(new java.sql.Date(createdDateCriteria.getTime()));
    }

    // First occurrence condition
    sql.append("AND r.rejectedUserId IN ( ");
    sql.append("  SELECT MIN(u.rejectedUserId) ");
    sql.append("  FROM RejectedUser u ");
    sql.append("  GROUP BY u.srcUserId, u.extractSystem ");
    sql.append(") ");

    sql.append("ORDER BY r.createdDate DESC ");

    // Oracle-specific row limit
    sql.insert(0, "SELECT * FROM (");
    sql.append(") WHERE ROWNUM <= 100");

    return jdbcTemplate.query(sql.toString(), params.toArray(), new BeanPropertyRowMapper<>(RejectedUser.class));
}
