```
public List<RejectedUser> findByValue(String srcUserId, Long extrctSysId,
                                      String selectedDateCriteria, Date createdDateCriteria) {

    return this.hibernateTemplate.execute(session -> {
        StringBuilder sb = new StringBuilder(256);
        sb.append("SELECT r FROM RejectedUser r ");
        sb.append("WHERE r.deleteFlag <> 'Y' ");
        sb.append("AND r.extractSystem.extrctSysId <> 9 ");

        if (srcUserId != null && !srcUserId.isEmpty()) {
            sb.append("AND r.srcUserId LIKE :srcUserId ");
        }

        if (extrctSysId != null && extrctSysId != -1) {
            sb.append("AND r.extractSystem.extrctSysId = :extrctSysId ");
        }

        // Sanitize and apply date filter
        if (createdDateCriteria != null) {
            // Whitelist only valid operators
            switch (selectedDateCriteria) {
                case "gt":
                    sb.append("AND trunc(r.createdDate) > :createdDate ");
                    break;
                case "lt":
                    sb.append("AND trunc(r.createdDate) < :createdDate ");
                    break;
                case "eq":
                    sb.append("AND trunc(r.createdDate) = :createdDate ");
                    break;
                default:
                    // Invalid operator, optionally throw an exception or skip the filter
                    break;
            }
        }

        sb.append("AND r.rejectedUserId IN ( ");
        sb.append("SELECT MIN(u.rejectedUserId) FROM RejectedUser u ");
        sb.append("GROUP BY u.srcUserId, u.extractSystem.extrctSysId) ");

        sb.append("ORDER BY r.createdDate DESC");

        Query<RejectedUser> query = session.createQuery(sb.toString(), RejectedUser.class);

        if (srcUserId != null && !srcUserId.isEmpty()) {
            query.setParameter("srcUserId", "%" + srcUserId + "%");
        }
        if (extrctSysId != null && extrctSysId != -1) {
            query.setParameter("extrctSysId", extrctSysId);
        }
        if (createdDateCriteria != null &&
            ("gt".equals(selectedDateCriteria) ||
             "lt".equals(selectedDateCriteria) ||
             "eq".equals(selectedDateCriteria))) {
            query.setParameter("createdDate", createdDateCriteria);
        }

        query.setMaxResults(100);
        return query.list();
    });
}
