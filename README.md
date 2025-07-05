```
public List<RejectedUser> findByValue(String srcUserId, Long extrctSysId,
        String selectedDateCriteria, Date createdDateCriteria) {

    StringBuilder sb = new StringBuilder(256);
    sb.append(" SELECT r FROM RejectedUser as r ");
    sb.append(" WHERE r.deleteFlag <> 'Y' ");
    sb.append(" AND r.extractSystem.extrctSysId <> 9 ");

    if (srcUserId != null && !srcUserId.isEmpty()) {
        sb.append(" AND r.srcUserId like :srcUserId ");
    }
    if (extrctSysId != null && extrctSysId != -1) {
        sb.append(" AND r.extractSystem.extrctSysId = :extrctSysId ");
    }
    if (createdDateCriteria != null) {
        switch (selectedDateCriteria) {
            case "gt":
                sb.append(" AND trunc(r.createdDate) > :createdDate ");
                break;
            case "lt":
                sb.append(" AND trunc(r.createdDate) < :createdDate ");
                break;
            case "eq":
                sb.append(" AND trunc(r.createdDate) = :createdDate ");
                break;
        }
    }

    sb.append(" AND r.rejectedUserId in ( ");
    sb.append(" SELECT MIN (u.rejectedUserId) FROM RejectedUser as u ");
    sb.append(" GROUP BY u.srcUserId, u.extractSystem.extrctSysId) ");
    sb.append(" ORDER BY r.createdDate DESC ");

    return this.hibernateTemplate.execute((session) -> {
        org.hibernate.query.Query query = session.createQuery(sb.toString());

        if (srcUserId != null && !srcUserId.isEmpty()) {
            query.setParameter("srcUserId", "%" + srcUserId + "%");
        }
        if (extrctSysId != null && extrctSysId != -1) {
            query.setParameter("extrctSysId", extrctSysId);
        }
        if (createdDateCriteria != null) {
            query.setParameter("createdDate", createdDateCriteria);
        }

        query.setMaxResults(100);
        return query.list();
    });
}
