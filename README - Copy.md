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

    // Implementation of this method to be used from acoms-client jar
    public ResponseWrapper<ByteWrapper> generateMcsForGroup(MCSRequestDTO mcsRequest, ACOMSRequest acomsRequest, HttpServletRequest httpRequest) {
        return null;
    }

    private ResponseWrapper<ByteWrapper> handleResponse(HttpResponse httpResponse) throws Exception {
        ResponseWrapper<ByteWrapper> responseWrapper;
        int statusCode = httpResponse.getStatusLine().getStatusCode();

        if (statusCode >= 200 && statusCode < 300) {
            HttpEntity entity = httpResponse.getEntity();
            String response = EntityUtils.toString(entity, "UTF-8");
            responseWrapper = buildResponse(response);
        } else {

            throw new Exception("Error accessing ACOMS received error code: " + statusCode);
        }
        return responseWrapper;
    }

    public ResponseWrapper<ByteWrapper> buildResponse(String response) throws Exception {
        ResponseWrapper<ByteWrapper> responseWrapper = new ResponseWrapper<>();

        Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(new InputSource(new StringReader(response)));

        NodeList respNodes = doc.getElementsByTagName("responseWrapper");
        if (respNodes.getLength() > 0) {
            Element respElement = (Element) respNodes.item(0);

            String respCode = respElement.getElementsByTagName("responseCode").item(0).getTextContent();

            String respMessage = respElement.getElementsByTagName("responseMessage").item(0).getTextContent();
            if (respCode != null) {
                if (respCode.equalsIgnoreCase("error"))
                    responseWrapper.setResponseCode(ResponseCode.ERROR);
                if (respCode.equalsIgnoreCase("success"))
                    responseWrapper.setResponseCode(ResponseCode.SUCCESS);
                if (respCode.equalsIgnoreCase("warning")) {
                    responseWrapper.setResponseCode(ResponseCode.WARNING);
                }
            } else {
                responseWrapper.setResponseCode(ResponseCode.NOT_FOUND);
            }
            responseWrapper.setResponseMessage(respMessage);

            byte[] bytearray = response.getBytes(StandardCharsets.UTF_8);
            ByteWrapper payloadWrapper = new ByteWrapper(bytearray);

            responseWrapper.setPayload(payloadWrapper);
        }

        return responseWrapper;
    }