```
package slf.com.dependencyFinder;

import org.apache.poi.xssf.usermodel.XSSFRow;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import slf.com.dependencyFinder.utility.DependencyFinderConstant;
import slf.com.dependencyFinder.utility.KnowYourProject;

import java.io.BufferedReader;
import java.io.File;
import java.nio.file.Files;
import java.util.*;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.util.stream.IntStream;

public class DetectTestClasses {

    // Shared context (must be the same instance used elsewhere in the app)
    private final KnowYourProject context;
    private static final Logger logger = LoggerFactory.getLogger(DetectTestClasses.class);

    public DetectTestClasses(KnowYourProject context) {
        this.context = Objects.requireNonNull(context, "KnowYourProject context required");
    }

    public void checkTestClassesInProject(File file) {
        if (file == null || !file.isFile()) return;

        Set<String> found = new HashSet<>();
        boolean inTestClass = false;
        boolean junit3Mode = false;

        String currentClass = null;
        int methodCount = 0;

        boolean importTestCase = false;

        Pattern classNamePattern = Pattern.compile("\\bclass\\s+(\\w+)");
        Pattern junit3ExtendsPattern = Pattern.compile("class\\s+(\\w+)\\s+extends\\s+TestCase");
        Pattern junit4ClassPattern = Pattern.compile(".*\\bclass\\b.*Test\\b.*");
        Pattern junit3MethodPattern = Pattern.compile("\\bpublic\\s+\\w.*\\s+test\\w*\\s*\\(");

        try (BufferedReader br = Files.newBufferedReader(file.toPath())) {
            String line;

            while ((line = br.readLine()) != null) {
                String l = line.trim();

                // -------- Detect JUnit-3 class: extends TestCase --------
                Matcher junit3Ext = junit3ExtendsPattern.matcher(l);
                if (junit3Ext.find()) {
                    inTestClass = true;
                    junit3Mode = true;
                    currentClass = junit3Ext.group(1);
                    continue;
                }

                // -------- Detect JUnit-4/5/TestNG class ending with Test --------
                if (junit4ClassPattern.matcher(l).matches()) {
                    inTestClass = true;
                    junit3Mode = false;

                    Matcher m = classNamePattern.matcher(l);
                    if (m.find()) currentClass = m.group(1);
                }

                // -------- Method-level detection --------
                if (inTestClass) {
                    // JUnit-4/5/TestNG
                    if (l.startsWith("@Test")) {
                        methodCount++;
                    }
                    // JUnit-3 (testXXX methods)
                    else if (junit3Mode && junit3MethodPattern.matcher(l).find()) {
                        methodCount++;
                    }
                }

                // -------- End of class (rough but effective) --------
                if (l.equals("}") && inTestClass) {
                    if (currentClass != null && methodCount > 0) {
                        found.add(currentClass + " (methods: " + methodCount + ")");
                    }
                    inTestClass = false;
                    junit3Mode = false;
                    currentClass = null;
                    methodCount = 0;
                }
            }

            // -------- Handle EOF without closing brace --------
            if (inTestClass && currentClass != null && methodCount > 0) {
                found.add(currentClass + " (methods: " + methodCount + ")");
            }

            System.out.println("Detected test classes in " + file.getPath() + ": " + found);

        } catch (Exception e) {
            logger.debug("Test detection failed for: {}", file.getPath());
        }

        if(!found.isEmpty()) {
            context.getProjectInfo().getTestClassesMap()
                    .computeIfAbsent(file.getPath(), k -> new HashSet<>())
                    .addAll(found);
        }
    }

    public void populateTestInfo(XSSFWorkbook workbook) {
        Map<String, Set<String>> testMap = context.getProjectInfo().getTestClassesMap();
        if (testMap == null || testMap.isEmpty()) return;

        XSSFSheet sheet = workbook.createSheet("Test Classes");
        AtomicInteger rowIdx = new AtomicInteger();

        int maxTests = testMap.values().stream().mapToInt(Set::size).max().orElse(1);
        List<String> heading = new ArrayList<>(List.of(
                DependencyFinderConstant.FILE_NAME,
                "Class Count"
        ));
        for (int i = 1; i <= maxTests; i++) heading.add("Test Class " + i);
        context.createExcelHeading(workbook, sheet, rowIdx.getAndIncrement(), heading);

        int totalClasses = 0;
        int totalMethods = 0;

        for (Map.Entry<String, Set<String>> e : testMap.entrySet()) {
            XSSFRow row = sheet.createRow(rowIdx.getAndIncrement());
            int col = 0;
            List<String> sorted = e.getValue().stream().sorted().toList();
            int classCount = sorted.size();
            totalClasses += classCount;

            // Sum method counts from string pattern "(methods: N)"
            for (String s : sorted) {
                Matcher m = Pattern.compile("\\(methods:\\s*(\\d+)\\)").matcher(s);
                if (m.find()) totalMethods += Integer.parseInt(m.group(1));
            }

            context.setCellvalueInExcel(e.getKey(), row.createCell(col++), false, workbook);
            context.setCellvalueInExcel(classCount, row.createCell(col++), false, workbook);

            for (String testClass : sorted) {
                context.setCellvalueInExcel(testClass, row.createCell(col++), false, workbook);
            }
        }

        // Summary rows
        XSSFRow summary1 = sheet.createRow(rowIdx.getAndIncrement());
        context.setCellvalueInExcel("Total Test Classes", summary1.createCell(0), true, workbook);
        context.setCellvalueInExcel(totalClasses, summary1.createCell(1), false, workbook);

        XSSFRow summary2 = sheet.createRow(rowIdx.getAndIncrement());
        context.setCellvalueInExcel("Total Test Methods", summary2.createCell(0), true, workbook);
        context.setCellvalueInExcel(totalMethods, summary2.createCell(1), false, workbook);

        IntStream.range(0, heading.size()).forEach(sheet::autoSizeColumn);
    }
}

```
