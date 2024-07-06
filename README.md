# files
local files added in this repo

      ## Xstream

      XStream xstream = new XStream(new JettisonMappedXmlDriver());
			xstream.alias("transactionReplyDTO", TransactionReplyDTO.class);
			xstream.addImplicitCollection(TransactionReplyDTO.class, "events", TransactionEventDTO.class);
			xstream.addPermission(NoTypePermission.NONE);
			xstream.addPermission(NullPermission.NULL);
			xstream.addPermission(PrimitiveTypePermission.PRIMITIVES);
			xstream.allowTypesByWildcard(new String[] {
					TransactionReplyDTO.class.getPackage().getName()+".*"
			});

     ##
     grep -r 'oaemployerc'



public ResponseWrapper<ByteWrapper> generateMcsForMember(MCSRequestDTO mcsRequest, ACOMSRequest acomsRequest, HttpServletRequest httpRequest) {
        ResponseWrapper<ByteWrapper> responseWrapper;
        try {
            URIBuilder uriBuilder = new URIBuilder(getServiceUrl());
            String path = uriBuilder.build().getPath() + "/billing/" +
                    "mcs/" + "member/" +
                    acomsRequest.getInsuranceSystem().name() + "/" +
                    acomsRequest.getRole().name() + "/" +
                    acomsRequest.getUserName() + "/" +
                    acomsRequest.getSesnGuid() + "/" +
                    acomsRequest.getCommunicationChnnl().name();

            String reportName = mcsRequest.getReportName();
            if (reportName == null) {
                reportName = "";
            }

            uriBuilder.setPath(path)
                    .addParameter("policyNumber", mcsRequest.getPolicyNumber())
                    .addParameter("billGroups", mcsRequest.getBillGroupString())
                    .addParameter("caseMemberKeys", mcsRequest.getCaseMemberKeysString())
                    .addParameter("memberNumber", mcsRequest.getMemberNumber())
                    .addParameter("deductionFrequencies", DeductionFrequency.getFrequenciesAsString(mcsRequest.getDeductionFrequencies()))
                    .addParameter("effectiveDate", mcsRequest.getEffectiveDate())
                    .addParameter("reportName", URLEncoder.encode(reportName, StandardCharsets.UTF_8))
                    .addParameter("reportTypeName", mcsRequest.getReportType().name())
                    .addParameter("externalViewable", String.valueOf(mcsRequest.isExternalViewable()))
                    .addParameter("externalUser", String.valueOf(mcsRequest.isExternalUser()));

            String baseReqUrl = uriBuilder.build().toURL().toString();


            HttpUriRequest uriRequest = RequestBuilder.get().setUri(baseReqUrl).addHeader("Content-Type", "application/xml").addHeader("cookie", getSiteminderCookie(httpRequest)).build();

            logger.info("URI for generateMcsForMember  : {}", uriRequest.getURI());
            HttpClient defaultHttpClient = HttpClientBuilder.create().build();

            HttpResponse httpResponse = defaultHttpClient.execute(uriRequest);

            responseWrapper = handleResponse(httpResponse);

            String payload = new String((responseWrapper.getPayload()).bytes);
            int start = payload.indexOf("<bytes>");
            int end = payload.indexOf("</bytes>");
            String justPDF = payload.substring(start + "<bytes>".length(), end);
            Base64 decoder = new Base64();
            byte[] decodedPDF = decoder.decode(justPDF);
            responseWrapper.setPayload(new ByteWrapper(decodedPDF));
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
        return responseWrapper;
    }
