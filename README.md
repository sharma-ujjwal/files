```
package slf.com.dependencyFinder;

import org.apache.poi.xssf.usermodel.*;
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

    private final KnowYourProject context;
    private static final Logger logger = LoggerFactory.getLogger(DetectTestClasses.class);

    public DetectTestClasses(KnowYourProject context) {
        this.context = Objects.requireNonNull(context);
    }

    // Model
    public static class TestClassInfo {
        public final String className;
        public final List<String> testMethods = new ArrayList<>();
        public TestClassInfo(String className) { this.className = className; }
        @Override public String toString() { return className + " (methods: " + testMethods.size() + ")"; }
    }

    // Entry point
    public void checkTestClassesInProject(File file) {
        if (file == null || !file.isFile() || !file.getName().endsWith(".java")) return;
        List<TestClassInfo> detected = detectTestClasses(file);
        if (detected.isEmpty()) return;
        // FIX: use Set<TestClassInfo> not List<TestClassInfo>
        context.getProjectInfo().getTestClassesMap()
                .computeIfAbsent(file.getPath(), k -> new LinkedHashSet<>())
                .addAll(detected);
    }

    // Detection
    private List<TestClassInfo> detectTestClasses(File file) {
        List<TestClassInfo> results = new ArrayList<>();
        try (BufferedReader br = Files.newBufferedReader(file.toPath())) {
            String line;
            boolean inTestClass = false;
            boolean junit3Mode = false;
            boolean lastLineWasAnnotation = false;

            String currentClass;
            TestClassInfo currentInfo = null;

            Pattern classNamePattern = Pattern.compile("\\bclass\\s+(\\w+)");
            Pattern junit3ExtendsPattern = Pattern.compile("class\\s+(\\w+)\\s+extends\\s+TestCase");
            Pattern junitSuffixClassPattern = Pattern.compile("\\bclass\\s+\\w*Test\\b");
            Pattern junit3MethodPattern = Pattern.compile("\\bpublic\\s+\\w.*\\s+(test\\w*)\\s*\\(");
            Pattern methodNamePattern = Pattern.compile("\\bpublic\\s+\\w.*\\s+(\\w+)\\s*\\(");

            while ((line = br.readLine()) != null) {
                String trimmed = line.trim();

                // JUnit3 class
                Matcher j3 = junit3ExtendsPattern.matcher(trimmed);
                if (j3.find()) {
                    inTestClass = true;
                    junit3Mode = true;
                    currentClass = j3.group(1);
                    currentInfo = new TestClassInfo(currentClass);
                    continue;
                }

                // Class ending with Test (JUnit4/5/TestNG naming)
                if (!inTestClass && junitSuffixClassPattern.matcher(trimmed).find()) {
                    inTestClass = true;
                    junit3Mode = false;
                    Matcher m = classNamePattern.matcher(trimmed);
                    if (m.find()) {
                        currentClass = m.group(1);
                        currentInfo = new TestClassInfo(currentClass);
                    }
                }

                // @Test annotation (simple form)
                if (trimmed.startsWith("@Test")) {
                    lastLineWasAnnotation = true;
                    continue;
                }

                // JUnit4/5/TestNG method
                if (inTestClass && lastLineWasAnnotation) {
                    Matcher m = methodNamePattern.matcher(trimmed);
                    if (m.find() && currentInfo != null) currentInfo.testMethods.add(m.group(1));
                    lastLineWasAnnotation = false;
                }

                // JUnit3 methods
                if (inTestClass && junit3Mode) {
                    Matcher m = junit3MethodPattern.matcher(trimmed);
                    if (m.find() && currentInfo != null) currentInfo.testMethods.add(m.group(1));
                }

                // Class end
                if (trimmed.equals("}")) {
                    if (inTestClass && currentInfo != null && !currentInfo.testMethods.isEmpty()) {
                        results.add(currentInfo);
                    }
                    inTestClass = false;
                    junit3Mode = false;
                    currentClass = null;
                    currentInfo = null;
                    lastLineWasAnnotation = false;
                }
            }
        } catch (Exception e) {
            logger.debug("Test detection failed for: {}", file.getPath(), e);
        }
        return results;
    }

    // Excel output
    public void populateTestInfo(XSSFWorkbook workbook) {
        Map<String, Set<TestClassInfo>> testMap = context.getProjectInfo().getTestClassesMap();
        if (testMap == null || testMap.isEmpty()) return;

        XSSFSheet sheet = workbook.createSheet("Test Classes");
        AtomicInteger rowIdx = new AtomicInteger();

        int maxClasses = testMap.values().stream().mapToInt(Set::size).max().orElse(1);

        List<String> heading = new ArrayList<>(List.of(
                DependencyFinderConstant.FILE_NAME,
                "Test Class Count"
        ));
        for (int i = 1; i <= maxClasses; i++) heading.add("Test Class " + i);

        context.createExcelHeading(workbook, sheet, rowIdx.getAndIncrement(), heading);

        int totalClasses = 0;
        int totalMethods = 0;

        for (Map.Entry<String, Set<TestClassInfo>> e : testMap.entrySet()) {
            XSSFRow row = sheet.createRow(rowIdx.getAndIncrement());
            int col = 0;

            List<TestClassInfo> sorted = e.getValue().stream()
                    .sorted(Comparator.comparing(t -> t.className))
                    .toList();

            totalClasses += sorted.size();
            for (TestClassInfo info : sorted) totalMethods += info.testMethods.size();

            context.setCellvalueInExcel(e.getKey(), row.createCell(col++), false, workbook);
            context.setCellvalueInExcel(sorted.size(), row.createCell(col++), false, workbook);

            for (TestClassInfo info : sorted) {
                context.setCellvalueInExcel(
                        info.className + " (methods: " + info.testMethods.size() + ")",
                        row.createCell(col++), false, workbook
                );
            }
        }

        XSSFRow s1 = sheet.createRow(rowIdx.getAndIncrement());
        context.setCellvalueInExcel("Total Test Classes", s1.createCell(0), true, workbook);
        context.setCellvalueInExcel(totalClasses, s1.createCell(1), false, workbook);

        XSSFRow s2 = sheet.createRow(rowIdx.getAndIncrement());
        context.setCellvalueInExcel("Total Test Methods", s2.createCell(0), true, workbook);
        context.setCellvalueInExcel(totalMethods, s2.createCell(1), false, workbook);

        IntStream.range(0, heading.size()).forEach(sheet::autoSizeColumn);
    }
}
