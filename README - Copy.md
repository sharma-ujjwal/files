```
public List<ExceptionSummary> findExceptions(boolean active) {
		StringBuilder sql = new StringBuilder();
		sql.append(" SELECT");
		sql.append("   u.USER_ID,");
		sql.append("   u.FIRST_NM,");
		sql.append("   u.LAST_NM,");
		sql.append("   u.MDDL_NM,");
		sql.append("   u.DIVISN_ID,");
		sql.append("   u.DIVISN_NAME,");
		sql.append("   u.DPRTMNT_ID,");
		sql.append("   d.DPRTMNT_NM_CST_CNTR,");
		sql.append("   u.SUPERVISOR_ID,");
		sql.append("   u.SUPERVISOR,");
		sql.append("   u.JOB_TITLE,");
		sql.append("   u.USR_TYPE_ID,");
		sql.append("   u.USER_TYPE,");
		sql.append("   u.USR_STATUS_ID,");
		sql.append("   u.STATUS,");
		sql.append("   u.EMAIL_ADDR,");
		sql.append("   u.PHONE,");
		sql.append("   u.KEY_ID,");
		sql.append("   u.LOCATION");
		sql.append(" FROM SDY025_USER u");
		sql.append(" INNER JOIN SDY023_DPRTMNT d on d.DPRTMNT_ID = u.DPRTMNT_ID");
		sql.append(" INNER JOIN SDY082_USR_STATUS s on ");
		sql.append("   (");
		sql.append("     s.usr_status_id = u.usr_status_id");

		if (active) {
			sql.append("     AND s.USR_STATUS_DESCR in ('ACTIVE', 'LEAVE', 'UNKNOWN', null)");
		} else {
			sql.append("     AND s.USR_STATUS_DESCR in ('INACTIVE/RETIRED')");
		}

		sql.append("   )");
		sql.append(" WHERE ");
		sql.append("   (");
		sql.append("     u.DIVISN_ID = -1");
		sql.append("     OR u.DPRTMNT_ID = -1");
		sql.append("     OR u.SUPERVISOR_ID = -1");
		sql.append("     OR u.USR_TYPE_ID = -1");
		sql.append("     OR u.USR_STATUS_ID = -1");

		// users do not want to see -1 values if only email has a -1 so this
		// part of the where clause is only important for active
		if (active) {
			sql.append("     OR u.EMAIL_ADDR = '-1'");
		}
		
		sql.append("   )");
		sql.append(" AND"); 
		sql.append("   (");
		sql.append("     u.DLT_FLG <> 'Y'");
		sql.append("     AND u.ACTV_TO_DT = ?0");
		sql.append("    )");

		List<ExceptionSummary> exceptions = this.jdbcTemplate.query(sql.toString(),
				new Object[] { DateUtil.END_OF_TIME },
				new ExceptionSummaryRowMapper());
		return exceptions;
	}
