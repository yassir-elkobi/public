private void populateField(JSONObject output, Map<String, Object> itemMap, String k,
                            String identifier, String priceExpression,
                            String instrumentType, String requestDate, String code) {
    if (k.contains("-")) {
        if (!instrumentType.isEmpty() && code.equalsIgnoreCase(FUNDS)) {
            // FUNDS compound field: instrumentType = "VALUE1|VALUE2"
            String[] parts = instrumentType.split("\\|");
            if (parts.length > 1) {
                output.put(k, itemMap.get(identifier.toUpperCase()) + "-" +
                        itemMap.get(parts[1].toUpperCase()));
            } else {
                output.put(k, itemMap.get(identifier.toUpperCase()));
            }
        } else {
            // Factset compound field like "Synonym Code-Market Place" or "Synonym Code-Currency"
            // Left part = identifier value (e.g. CA62930A1021)
            // Right part = the field name after "-" mapped to itemMap key (e.g. MARKET_PLACE → 061)
            String[] parts = k.split("-", 2);
            Object identifierVal = itemMap.get(identifier.toUpperCase());

            if (parts.length > 1) {
                String rightKey = parts[1].trim().replace(' ', '_').toUpperCase();
                Object rightVal = itemMap.get(rightKey);

                if (rightVal != null) {
                    // e.g. "Synonym Code-Market Place" → "CA62930A1021-061"
                    output.put(k, identifierVal + "-" + rightVal);
                } else {
                    // right part key not found in itemMap — use identifier only
                    log.debug("populateField: right-side key '{}' not found in itemMap for field '{}'",
                            rightKey, k);
                    output.put(k, identifierVal);
                }
            } else {
                output.put(k, identifierVal);
            }
        }
    } else if (k.equalsIgnoreCase(PRICE_EXPRESSION.replace("_", " "))) {
        output.put(k, priceExpression);
    } else if (k.equalsIgnoreCase(SYNONYM_CODE)) {
        output.put(k, itemMap.get(identifier.toUpperCase()));
    } else if (k.equalsIgnoreCase(INSTRUMENT_TYPE)) {
        output.put(k, InstrumentTypeMapper.getDisplayValue((String) itemMap.get(INSTRUMENT)));
    } else if (k.equalsIgnoreCase(DATE)) {
        // Process H: uses historical date. OnDemand (U/I): uses yesterday.
        output.put(k, requestDate);
    } else {
        output.put(k, itemMap.get(k.replace(' ', '_').toUpperCase()));
    }
}





security,instrument,marketPlace,currency,pricingFlag,staticFlag,derivedFlag,internalID,noval,sedolHiport,hpSydney,dimensionId,bloombergTicker,factsetId,subInstrument,priceExpression,bloomLuxFutOpt,factsetLuxFutOpt,HISTORICAL_DATE
CA62930A1021,EQUITY,061,CAD,Y,Y,Y,13385780,,,,,,,EQUITY,20,,,20260430
