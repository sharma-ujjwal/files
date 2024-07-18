public class TransactionHistoryActionListenerBean extends EmployerBean {
	private void retrieveTransactionEvents(TransactionRequestDTO requestDTO){
		logger.debug("TransactionHistoryActionListenerBean: retrieveTransactionEvents");
		try {
			/*
			 * We have to add one does to the requested date range b/c
			 * the transaction query looks for dates less than the selected
			 * date.
			 */
			Calendar calendar = new GregorianCalendar();
			calendar.setTime(getTransactionHistoryDataBean().getEndDateRange());
			calendar.add(Calendar.DAY_OF_MONTH, 1);

			requestDTO.setEndDate(formatForDisplay(calendar.getTime()));

			requestDTO.setCurrentStatusOnly("y");
			JSONObject request = createJSONObjectRequest("transactionRequestDTO", requestDTO, getUserBean().getUserName(), "transactionEventManager");
			JSONObject response = executeMiddlewareServiceRequest(request);
			XStream xstream = new XStream(new JettisonMappedXmlDriver());
			xstream.alias("transactionReplyDTO", TransactionReplyDTO.class);
			xstream.addImplicitCollection(TransactionReplyDTO.class, "events", TransactionEventDTO.class);
			xstream.addPermission(NoTypePermission.NONE);
			xstream.addPermission(NullPermission.NULL);
			xstream.addPermission(PrimitiveTypePermission.PRIMITIVES);
			xstream.allowTypesByWildcard(new String[] {
					TransactionReplyDTO.class.getPackage().getName()+".*"
			});
			TransactionReplyDTO transactionReplyDTO = (TransactionReplyDTO) xstream.fromXML(response.toString());
			if(transactionReplyDTO != null && transactionReplyDTO.getEvents() != null) {
				getTransactionHistoryDataBean().setEvents(transactionReplyDTO.getEvents());
			} else{
				TransactionEventDTO dto = new TransactionEventDTO();
				dto.setFirstName("");
				dto.setLastName("");
				dto.setMemberNumber("");
				List<TransactionEventDTO> list = new ArrayList<TransactionEventDTO>(1);
				list.add(dto);
				getTransactionHistoryDataBean().setEvents(list);
			}
			transactionEventFilter();
			groupByDate();
			groupByMember();
		}catch (Exception e) {
			logger.error("retrieveTransactionEvents {}" , e);
		}
	}
}

public class EmployerBean {
	protected static JSONObject createJSONObjectRequest(String alias, Object requestObject, String userId, String manager) {
		try {
			JSONObject formattedRequest = new JSONObject();

			XStream xstream = new XStream(new JsonHierarchicalStreamDriver());
			xstream.setMode(XStream.NO_REFERENCES);
			xstream.alias(alias, requestObject.getClass());
			String test = xstream.toXML(requestObject);
			JSONObject request = new JSONObject(test);
			request.put(JsonConstants.JSON_USER_ID, userId);

			formattedRequest.put(JsonConstants.JSON_MANAGER, manager);
			formattedRequest.put(JsonConstants.JSON_REQUEST, request);

			return formattedRequest;

		} catch (Exception e) {
			return new JSONObject();
		}
	}
}