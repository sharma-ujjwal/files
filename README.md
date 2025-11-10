```
public void populateCacheInfo(XSSFWorkbook workbook) {
        Map<String, Set<String>> cacheMap = getProjectInfo().getCacheConfigMap();
        if (cacheMap.isEmpty()) return;

        XSSFSheet sheet = workbook.createSheet("Cache Configuration");
        int rowIdx = 0;

        List<String> heading = List.of(DependencyFinderConstant.FILE_NAME, "Cache Names", DependencyFinderConstant.COUNT);
        createExcelHeading(workbook, sheet, rowIdx++, heading);

        for (Map.Entry<String, Set<String>> e : cacheMap.entrySet()) {
            XSSFRow row = sheet.createRow(rowIdx++);
            int col = 0;
            setCellvalueInExcel(e.getKey(), row.createCell(col++), false, workbook);
            String names = e.getValue().stream().sorted().collect(Collectors.joining(", "));
            setCellvalueInExcel(names, row.createCell(col++), false, workbook);
            setCellvalueInExcel(e.getValue().size(), row.createCell(col), false, workbook);
        }

        int total = cacheMap.values().stream().mapToInt(Set::size).sum();
        XSSFRow summary = sheet.createRow(rowIdx++);
        setCellvalueInExcel("Total", summary.createCell(0), true, workbook);
        setCellvalueInExcel("", summary.createCell(1), false, workbook);
        setCellvalueInExcel(total, summary.createCell(2), false, workbook);
    }
