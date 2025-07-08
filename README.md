```
Unsanitized input from a database flows into execute, where it is used in an SQL query. This may result in an SQL Injection vulnerability.

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
