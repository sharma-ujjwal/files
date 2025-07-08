```
StringBuilder sb = new StringBuilder(256);
		sb.append(" SELECT r FROM RejectedUser as r ");
		sb.append(" WHERE r.deleteFlag <> 'Y'  ");
		sb.append(" AND r.extractSystem <> 9  ");
		if (!"".equals(srcUserId) && srcUserId != null) {
			sb.append(" AND r.srcUserId like '%" + srcUserId + "%' ");
		}
		if (extrctSysId != null) {
			sb.append(" AND r.extractSystem.extrctSysId = " + extrctSysId );
		}
		if (createdDateCriteria != null) {

			if ("gt".equals(selectedDateCriteria)) {
				sb.append(" AND trunc(r.createdDate) > ? " );
			}
			if ("lt".equals(selectedDateCriteria)) {
				sb.append(" AND trunc(r.createdDate) < ? " );
			}
			if ("eq".equals(selectedDateCriteria)) {
				sb.append(" AND trunc(r.createdDate) = ? " );
			}
		}
		// to get the first occurrence this reject came in
		sb.append(" AND r.rejectedUserId in  ");
		sb.append(" (SELECT MIN (u.rejectedUserId) ");
		sb.append(" FROM RejectedUser as u GROUP BY u.srcUserId, u.extractSystem) ");
		sb.append(" ORDER BY r.createdDate DESC ");
        Object[] params = { createdDateCriteria} ;

		if (createdDateCriteria != null) 
			return this.hibernateTemplate.find(sb.toString(), params);
		else
			return this.hibernateTemplate.find(sb.toString());
