```
public List<PrivilegeComment> findByFilters(boolean isActive, String soxConcern,String applicationName,
			String privDescription,String privValue,Long functionDutyId, Long extractSystemId) {
		System.out.println("MYCARS soxDataAccess PrivilegeCommentDao.findByFilters() --> Start");
    	DetachedCriteria criteria = DetachedCriteria.forClass(PrivilegeComment.class);
    	if(isActive)
    		this.addDeleteActiveRestrictions(criteria);
		System.out.println("*** ***In Dao Extract sys id :"+ extractSystemId);
    	if(StringUtils.isNotEmpty(soxConcern))
    		criteria.add(Restrictions.like("soxConcern",soxConcern));
    	if(StringUtils.isNotEmpty(privDescription))
    		criteria.add(Restrictions.like("pk.description",privDescription,MatchMode.ANYWHERE).ignoreCase());
    	if(StringUtils.isNotEmpty(privValue))
    		criteria.add(Restrictions.like("pk.value",privValue,MatchMode.ANYWHERE).ignoreCase());
    	if(functionDutyId !=null && functionDutyId!=0)
    		criteria.add(Restrictions.eq("pk.functionDutyId", functionDutyId));
    	if(StringUtils.isNotEmpty(applicationName))
    		criteria.add(Restrictions.like("applicationName",applicationName,MatchMode.ANYWHERE).ignoreCase());
		if(extractSystemId !=null && extractSystemId!=-1) {
			System.out.println("*** In Dao Extract sys id :"+ extractSystemId);
			criteria.add(Restrictions.eq("extractSystemId", extractSystemId));
		}
		System.out.println("*** In Dao-----Extract sys id :"+ extractSystemId);
    	return (List<PrivilegeComment>) this.hibernateTemplate.findByCriteria(criteria, 0, 500);
    }
```
