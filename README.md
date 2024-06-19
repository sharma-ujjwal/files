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
