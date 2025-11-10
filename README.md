package abc.com.dependencyFinder.utility;

import com.sunlife.commonutils.CommonUtils;
import com.sunlife.commonutils.MavenUtils;
import com.sunlife.model.info.PackagingInfo;
import com.sunlife.model.pojos.repo.RepositoryInfo;
import com.sunlife.service.UtilServiceImpl;
import com.sunlife.service.app.GitCheckout;
import org.apache.commons.collections.CollectionUtils;
import org.apache.commons.lang3.StringUtils;
import org.apache.maven.model.Dependency;
import org.apache.poi.hssf.util.HSSFColor;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.util.StringUtil;
import org.apache.poi.xssf.usermodel.XSSFFormulaEvaluator;
import org.apache.poi.xssf.usermodel.XSSFRow;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;
import abc.com.dependencyFinder.utility.pojo.DetailedProjectInfo;
import abc.com.dependencyFinder.utility.pojo.ProjectCommonInfo;
import abc.com.dependencyFinder.utility.pojo.ProjectInfo;
import abc.com.dependencyFinder.utility.pojo.ProjectSizeInfo;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;
import java.io.*;
import java.nio.charset.Charset;
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.util.stream.Collectors;
import java.util.stream.IntStream;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.Set;
import java.util.HashSet;
import java.io.BufferedReader;

public class KnowYourProject {
    private final static Logger logger = LoggerFactory.getLogger(KnowYourProject.class);
    private static KnowYourProject knowYourProject;
    private static boolean antBuildProject = true;
    private ProjectInfo info = null;
    private String dbName;
    private Map<String, String> propertyFileMap = new ConcurrentHashMap<>();

    public void setPropertyFileMap(Map<String, String> propertyFileMap) {
        this.propertyFileMap = propertyFileMap;
    }

    public Map<String, String> getPropertyFileMap() {
        return propertyFileMap;
    }

    public String getDbName() {
        return dbName;
    }

    public void setDbName(String dbName) {
        this.dbName = dbName;
    }

    public ProjectInfo getProjectInfo() {
        Optional<ProjectInfo> isNull = Optional.ofNullable(info);
        if (isNull.isPresent()) {
            return info;
        } else {
            info = new ProjectInfo();
            return info;
        }
    }

    public void setProjectInfo(ProjectInfo projectInfo) {
        this.info = projectInfo;
    }
    public void setPackageName(String packageName) {
        this.packageName = packageName;
    }

    public String getPackageName() {
        return packageName;
    }

    private String packageName;

    public static KnowYourProject getKnowYourProject(boolean resetToDefault) {
        Optional<KnowYourProject> isNull = Optional.ofNullable(knowYourProject);
        if (isNull.isPresent() && !resetToDefault) {
            return knowYourProject;
        } else {
            knowYourProject = new KnowYourProject();
            return knowYourProject;
        }
    }

    private List<String> internalList = new ArrayList<String>();
    private List<String> externalList = new ArrayList<String>();

    public static void main(String st[]) {
    	Long startTime = System.currentTimeMillis();
		logger.info("Start Time of the utility " + System.currentTimeMillis());
        getKnowYourProject(false).startKYAExecution();
        startTime =  System.currentTimeMillis() - startTime;
		logger.info("Utility completed successfully in " + startTime/1000 +" seconds "+ startTime/100+" ms");
    }

    public void startKYAExecution() {
        Map<String, String> config = generatePropertyFileMap();
        commonUtils = new CommonUtils();
        String applicationRepoMasterSheetPath = Thread.currentThread().getContextClassLoader().getResource("").getPath() +
                config.get("repositoryURL");
        try {
            doRepoCheckout(commonUtils, config, applicationRepoMasterSheetPath);
        } catch (Exception e) {
            logger.error("Error in repo checkout", e);
        }
        processApplicationWise(commonUtils, applicationRepoMasterSheetPath, config);
    }
    public void initiateApplicationExecution(String applicationName) {
        String path = generatePropertyFileMap().get(DependencyFinderConstant.ROOT_DIR);
        logger.info("Start- execution for project path {}", path);
        readFile(path);
        //Populate/display data into excel sheet
        populateResultInExcel(applicationName);
        if (antBuildProject) {
            ExternalAntDependencyFinder externalDependencyAnalyzer = new ExternalAntDependencyFinder();
            ArrayList<Integer> externalJarCount = externalDependencyAnalyzer.beginAntExternalAnalyzer();
            InternalAntDependencyFinder fetchInternalDependency = new InternalAntDependencyFinder();
            ArrayList<Integer> internalJarCount = fetchInternalDependency.beginAntInternalDependency();
            externalDependencyAnalyzer.insertDependencyInfoInExcel(internalJarCount, externalJarCount);
            externalDependencyAnalyzer.insertPackagingInfoInExcel(internalJarCount, externalJarCount);
        }
    }

    public void doRepoCheckout(CommonUtils commonUtils, Map<String, String> config, String applicationRepoMasterSheetPath) {
        String dir = new GitCheckout().doRepoCheckout(config, commonUtils, applicationRepoMasterSheetPath);
        logger.info("Repo checkout done in directory : " + dir);
    }

    public void processApplicationWise(CommonUtils commonUtils, String checkoutRepoPath, Map<String, String> config) {
        List<RepositoryInfo> repositoryInfos = commonUtils.fetchRepoDetails(checkoutRepoPath);
        logger.info("Repository info size: " + repositoryInfos.size());
        Optional.ofNullable(repositoryInfos).filter(CollectionUtils::isNotEmpty).ifPresent(repoInfoList -> {
            repoInfoList.forEach((branchInfo) -> {
                try {
                    String checkoutFolderPath = config.get("gitCheckoutDir") + branchInfo.getProjectName();
                    config.put(DependencyFinderConstant.ROOT_DIR, checkoutFolderPath);
                    initiateApplicationExecution(branchInfo.getProjectName());
                    //resetting the value to default
                    resetToDefault();
                } catch (Exception e) {
                    resetToDefault();
                    logger.error("Error in processing application wise", e);
                }
            });
        });
    }

    public void resetToDefault() {
        this.commonUtils = new CommonUtils();
        getKnowYourProject(true);
        setProjectInfo(null);
    }
    private void createMissExcelHeading(XSSFWorkbook workbook, XSSFSheet spreadsheet,
                                        int atomicIntegerRowCount, List<String> headingList) {
        // end - for alignment
        Font boldFont = workbook.createFont();
        boldFont.setBold(true);
        boldFont.setColor(HSSFColor.HSSFColorPredefined.DARK_RED.getIndex());
        boldFont.setFontHeightInPoints((short) 15);

        CellStyle boldStyle = workbook.createCellStyle();
        boldStyle.setFont(boldFont);
        boldStyle.setAlignment(HorizontalAlignment.CENTER);
        boldStyle.setFillForegroundColor(IndexedColors.SKY_BLUE.getIndex());
        boldStyle.setFillPattern(FillPatternType.FINE_DOTS);
        // end - for alignment
        //this is for creating heading -start
        XSSFRow firstRow = spreadsheet.createRow(atomicIntegerRowCount);
        AtomicInteger atomicIntegerCellCount = new AtomicInteger();
        headingList.forEach(heading -> {
            Cell headingCell = firstRow.createCell(atomicIntegerCellCount.getAndIncrement());
            headingCell.setCellValue(heading);
            headingCell.setCellStyle(boldStyle);
        });
    }

    void createExcelHeading(XSSFWorkbook workbook, XSSFSheet spreadsheet,
                            int atomicIntegerRowCount, List<String> headingList) {
        // end - for alignment
        Font boldFont = workbook.createFont();
        boldFont.setBold(true);
        boldFont.setColor(HSSFColor.HSSFColorPredefined.DARK_YELLOW.getIndex());
        boldFont.setFontHeightInPoints((short) 13);

        CellStyle boldStyle = workbook.createCellStyle();
        boldStyle.setFont(boldFont);
        boldStyle.setAlignment(HorizontalAlignment.CENTER);
        boldStyle.setFillForegroundColor(IndexedColors.YELLOW.getIndex());
        boldStyle.setFillPattern(FillPatternType.FINE_DOTS);
        // end - for alignment
        //this is for creating heading -start
        XSSFRow firstRow = spreadsheet.createRow(atomicIntegerRowCount);
        AtomicInteger atomicIntegerCellCount = new AtomicInteger();
        headingList.forEach(heading -> {
            Cell headingCell = firstRow.createCell(atomicIntegerCellCount.getAndIncrement());
            headingCell.setCellValue(heading);
            headingCell.setCellStyle(boldStyle);
        });
    }
    public XSSFWorkbook getExistingSheet(String excelPath) {
        FileInputStream file = null;
        try {
            file = new FileInputStream(excelPath);
            return new XSSFWorkbook(file);
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
    public void removeExistingSheet(XSSFWorkbook workbook){
        removeBasedOnIndex(workbook);
    }
    int startIndex=0;
    public void removeBasedOnIndex(XSSFWorkbook workbook) {
        List<String> sheetNames = new ArrayList<>();
        Arrays.stream(Optional.of(generatePropertyFileMap().get(String.valueOf(DependencyEnum.sheetNames)))
                .get().split(",")).forEach(v -> sheetNames.add(v));
        sheetNames.forEach(sheetName -> {
            startIndex = 0;
            IntStream.range(0, workbook.getNumberOfSheets()).forEach(index -> {
                if (startIndex == 1) {

                } else {
                    int sheetIndex = workbook.getSheetIndex(sheetName);
                    if (sheetIndex != -1) {
                        startIndex = 1;
                        workbook.removeSheetAt(sheetIndex);
                    }
                }
            });
        });
    }
    public void populateResultInExcel(String applicationName) {
        String excelPath = Thread.currentThread().getContextClassLoader().getResource("").getPath() +
                generatePropertyFileMap().get(String.valueOf(DependencyEnum.excelFileName));
        XSSFWorkbook workbook = getExistingSheet(excelPath);
        //XSSFWorkbook workbook = new XSSFWorkbook();
        removeExistingSheet(workbook);
        populateKnownTechStack(workbook);
        populateUnKnownTechStack(workbook);
        populateApplicationPackagingType(workbook);
        populateApplicationBuildType(workbook);
        populateDependencyMap(workbook);
        populateDBDetails(workbook);
        populateWebServiceEndPoint(workbook);
        // Added: cache info sheet
        populateCacheInfo(workbook);
        createAndWriteInToExcelFile(workbook, createKYAOutputTemplate(applicationName));
    }

    public String createKYAOutputTemplate(String applicationName) {
        String destinationFile = Thread.currentThread().getContextClassLoader().getResource("").getPath() +
                generatePropertyFileMap().get("kyaOutputResourceName");
        try {
            Files.createDirectories(Paths.get(destinationFile.trim().substring(1)));
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        destinationFile = destinationFile + "/" + applicationName + "_" + generatePropertyFileMap().get("kyaOutput");
        return destinationFile;
    }
    public void populateWebServiceEndPoint(XSSFWorkbook workbook) {
        XSSFSheet spreadsheet = createSheet(workbook, DependencyFinderConstant.WEBSERVICE_DETAILS);
        AtomicInteger atomicIntegerRowCount = new AtomicInteger();

        List<String> firstHeadingList = new ArrayList<>(List.of(DependencyFinderConstant.WEBSERVICE_TYPE, DependencyFinderConstant.COUNT));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), firstHeadingList);
        Map<String, Integer> packagingCountInfo = populateApplicationCountMap(getProjectInfo().getWebServiceMap(), DependencyFinderConstant.WEBSERVICE_DETAILS, false);
        populateProjectDetails(packagingCountInfo, spreadsheet, atomicIntegerRowCount, 0, false, workbook);



        //this is for creating heading -start
        atomicIntegerRowCount.addAndGet(Integer.parseInt(generatePropertyFileMap().get(String.valueOf(DependencyEnum.blankRows))));
        List<String> headingList = new ArrayList<>(List.of(DependencyFinderConstant.FILE_NAME, DependencyFinderConstant.WEBSERVICE_TYPE,
                DependencyFinderConstant.COUNT, DependencyFinderConstant.WEBSERVICE_ENDPOINT));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), headingList);
        //this is for creating heading -end
        populateProjectDetails(getProjectInfo().getWebServiceMap(), spreadsheet, atomicIntegerRowCount, 0, false, workbook);
    }

    public void populateDBDetails(XSSFWorkbook workbook) {
        XSSFSheet spreadsheet = createSheet(workbook, DependencyFinderConstant.DB_CONNECTION_INFO);
        AtomicInteger atomicIntegerRowCount = new AtomicInteger();

        List<String> firstHeadingList = new ArrayList<>(List.of(DependencyFinderConstant.DATABASE_INFO, DependencyFinderConstant.COUNT));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), firstHeadingList);
        Map<String, Integer> packagingCountInfo = populateApplicationCountMap(getProjectInfo().getDbDetails(), DependencyFinderConstant.DB_CONNECTION_INFO, true);
        populateProjectDetails(packagingCountInfo, spreadsheet, atomicIntegerRowCount, 0, false, workbook);

        spreadsheet.createRow(atomicIntegerRowCount.getAndIncrement());

        List<String> secondHeadingList = new ArrayList<>(List.of(DependencyFinderConstant.DATABASE_TYPES, DependencyFinderConstant.COUNT));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), secondHeadingList);
        packagingCountInfo = populateApplicationCountMap(getProjectInfo().getDbDetails(), DependencyFinderConstant.DB_CONNECTION_INFO,false);
        populateProjectDetails(packagingCountInfo, spreadsheet, atomicIntegerRowCount, 0, false, workbook);

        //this is for creating heading -start
        atomicIntegerRowCount.addAndGet(Integer.parseInt(generatePropertyFileMap().get(String.valueOf(DependencyEnum.blankRows))));
        List<String> headingList = new ArrayList<>(List.of(DependencyFinderConstant.FILE_NAME, DependencyFinderConstant.DATABASE_NAME,
                DependencyFinderConstant.COUNT, DependencyFinderConstant.DATABASE_LIST));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), headingList);
        //this is for creating heading -end
        populateProjectDetails(getProjectInfo().getDbDetails(), spreadsheet, atomicIntegerRowCount, 0, false, workbook);
    }

    public void populateDependencyMap(XSSFWorkbook workbook) {
        XSSFSheet spreadsheet = createSheet(workbook, DependencyFinderConstant.DEPENDENCY_INFO);
        AtomicInteger atomicIntegerRowCount = new AtomicInteger();

        List<String> firstHeadingList = new ArrayList<>(List.of(DependencyFinderConstant.DEPENDENCY_TYPE, DependencyFinderConstant.COUNT));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), firstHeadingList);
        Map<String, Integer> packagingCountInfo = populateApplicationCountMap(getProjectInfo().getDependencyMap(), DependencyFinderConstant.DEPENDENCY_INFO,false);
        populateProjectDetails(packagingCountInfo, spreadsheet, atomicIntegerRowCount, 0, false, workbook);


        //this is for creating heading -start
        atomicIntegerRowCount.addAndGet(Integer.parseInt(generatePropertyFileMap().get(String.valueOf(DependencyEnum.blankRows))));
        List<String> headingList = new ArrayList<>(List.of(DependencyFinderConstant.FILE_NAME, DependencyFinderConstant.ARTIFACT_NAME
                , DependencyFinderConstant.DEPENDENCY_TYPE,
                DependencyFinderConstant.COUNT, DependencyFinderConstant.DEPENDENCY_LIST));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), headingList);
        //this is for creating heading -end
        populateProjectDetails(getProjectInfo().getDependencyMap(), spreadsheet, atomicIntegerRowCount, 0, false, workbook);
    }

    private void populateApplicationBuildType(XSSFWorkbook workbook) {
        XSSFSheet spreadsheet = createSheet(workbook, DependencyFinderConstant.APPLICATION_BUILD_TOOL);
        AtomicInteger atomicIntegerRowCount = new AtomicInteger();
        List<String> headingList = new ArrayList<>(List.of(DependencyFinderConstant.BUILD_TOOL));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), headingList);

        XSSFRow row = spreadsheet.createRow(atomicIntegerRowCount.getAndIncrement());
        AtomicInteger atomicIntegerCellCount = new AtomicInteger();
        int cellCount = atomicIntegerCellCount.getAndIncrement();
        Cell cell = row.createCell(cellCount);
        String buildTool = getProjectInfo().getApplicationBuildType();
        if (Optional.ofNullable(buildTool).isPresent()
                && Optional.ofNullable(buildTool).get().equalsIgnoreCase(DependencyFinderConstant.POM_XML)) {
            buildTool = DependencyFinderConstant.MAVEN;
        } else if (Optional.ofNullable(buildTool).isPresent()
                && Optional.ofNullable(buildTool).get().equalsIgnoreCase(DependencyFinderConstant.BUILD_GRADLE)) {
            buildTool = DependencyFinderConstant.GRADLE;
        } else {
            buildTool = DependencyFinderConstant.ANT;
        }
        this.setCellvalueInExcel(buildTool, cell, false, workbook);
    }

    private XSSFSheet createSheet(XSSFWorkbook workbook, String sheetName) {
        return workbook.createSheet(sheetName);
    }

    private void populateApplicationPackagingType(XSSFWorkbook workbook) {
        XSSFSheet spreadsheet = createSheet(workbook, DependencyFinderConstant.PACKAGING_INFO);
        AtomicInteger atomicIntegerRowCount = new AtomicInteger();
        List<String> firstHeadingList = new ArrayList<>(List.of(DependencyFinderConstant.PACKAGING_TYPE, DependencyFinderConstant.NUMBER_OF_PACKAGES));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), firstHeadingList);
        Map<String, Integer> packagingCountInfo = populateApplicationCountMap(getProjectInfo().getApplicationType(), DependencyFinderConstant.PACKAGING_INFO,false);
        populateProjectDetails(packagingCountInfo, spreadsheet, atomicIntegerRowCount, 0, false, workbook);

        //this will create another table in Excel.
        atomicIntegerRowCount.addAndGet(Integer.parseInt(generatePropertyFileMap().get(String.valueOf(DependencyEnum.blankRows))));
        List<String> secondHeadingList = new ArrayList<>(List.of(DependencyFinderConstant.PACKAGING_TYPE, DependencyFinderConstant.COUNT,
                DependencyFinderConstant.FILE_NAME, DependencyFinderConstant.ARTIFACT_NAME));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), secondHeadingList);
        populateProjectDetails(getProjectInfo().getApplicationType(), spreadsheet, atomicIntegerRowCount, 0, false, workbook);

    }

    public Map<String, Integer> createPackagingInfoCountMap1() {
        Map<String, Integer> packagingInfoCount = new ConcurrentHashMap<>();
        Optional.ofNullable(getProjectInfo().getApplicationType()).get().forEach((k, v) -> {
            packagingInfoCount.put(k, v.getApplicationPackagingType().size());
        });
        return packagingInfoCount;
    }

    public <K, V> Map<String, Integer> populateApplicationCountMap(Map<K, V> map, String type,boolean flag) {
        Map<String, Integer> finalMapCount = new ConcurrentHashMap<>();
        Optional.ofNullable(map).get().forEach((k, v) -> {
            if (type.equalsIgnoreCase(DependencyFinderConstant.PACKAGING_INFO) && v instanceof ProjectCommonInfo && k instanceof String) {
                finalMapCount.put((String) k, ((ProjectCommonInfo) v).getApplicationPackagingType().size());
            } else if (type.equalsIgnoreCase(DependencyFinderConstant.DEPENDENCY_INFO) && v instanceof ProjectCommonInfo) {
                getApplicationCount(finalMapCount, ((ProjectCommonInfo) v).getDependencyType(), flag);
            } else if ((type.equalsIgnoreCase(DependencyFinderConstant.WEBSERVICE_DETAILS) || type.equalsIgnoreCase(DependencyFinderConstant.DB_CONNECTION_INFO)) && v instanceof Map<?, ?>) {
                getApplicationCount(finalMapCount, (Map) v, flag);
            }
        });
        return finalMapCount;
    }

    public <K, V> void getApplicationCount(Map<String, Integer> outputMap, Map<K, V> iterateMap,boolean flag) {

        Optional.ofNullable(iterateMap).get().forEach((k, v) -> {

            if (k instanceof String && v instanceof List<?>) {
                String key = (String) k;
                if (flag) {
                    int dbCount = Optional.ofNullable(outputMap.isEmpty()).get().booleanValue() ? 0 : outputMap.get(DependencyFinderConstant.NUMBER_OF_DATABASES);
                    int dbTypes = Optional.ofNullable(outputMap.isEmpty()).get().booleanValue() ? 0 : outputMap.get(DependencyFinderConstant.NUMBER_OF_DATABASE_TYPES);
                    outputMap.put(DependencyFinderConstant.NUMBER_OF_DATABASE_TYPES, dbTypes + 1);
                    outputMap.put(DependencyFinderConstant.NUMBER_OF_DATABASES, dbCount + ((List<?>) v).size());
                }else if (outputMap.containsKey(key)) {
                    outputMap.put(key, outputMap.get(key) + ((List<?>) v).size());
                } else {
                    outputMap.put(key, ((List<?>) v).size());
                }
            } else if (k instanceof String && v instanceof Set<?>) {
                String key = (String) k;
                if (outputMap.containsKey(key)) {
                    outputMap.put(key, outputMap.get(key) + ((Set<?>) v).size());
                } else {
                    outputMap.put(key, ((Set<?>) v).size());
                }
            }
        });
    }
    private void populateKnownTechStack(XSSFWorkbook workbook) {
        XSSFSheet spreadsheet = createSheet(workbook, DependencyFinderConstant.KNOWN_TECHNOLOGY_INFO);
        AtomicInteger atomicIntegerRowCount = new AtomicInteger();
        //this is for creating heading -start
        List<String> headingList = new ArrayList<>(List.of(
                DependencyFinderConstant.TECHNOLOGY_USED, DependencyFinderConstant.NO_OF_FILE,
                DependencyFinderConstant.NO_OF_LINES, DependencyFinderConstant.NO_OF_METHOD_COUNT));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), headingList);
        //this is for creating heading -end
        populateProjectDetails(getProjectInfo().getTeckStackInfo(), spreadsheet, atomicIntegerRowCount, 0, true, workbook);
    }

    private void populateUnKnownTechStack(XSSFWorkbook workbook) {
        XSSFSheet spreadsheet = createSheet(workbook, DependencyFinderConstant.UNKNOWN_TECHNOLOGY_INFO);
        AtomicInteger atomicIntegerRowCount = new AtomicInteger();
        //this is for creating heading -start
        List<String> headingList = new ArrayList<>(List.of(
                DependencyFinderConstant.TECHNOLOGY_USED, DependencyFinderConstant.NO_OF_FILE,
                DependencyFinderConstant.NO_OF_LINES, DependencyFinderConstant.NO_OF_METHOD_COUNT));
        createExcelHeading(workbook, spreadsheet, atomicIntegerRowCount.getAndIncrement(), headingList);
        //this is for creating heading -end
        populateProjectDetails(getProjectInfo().getTeckStackInfo(), spreadsheet, atomicIntegerRowCount, 0, true, workbook);
    }


    public <T> void populateListInfo(AtomicInteger rowIndex, int cellIndex, XSSFSheet spreadsheet, T infoList) {
        //AtomicInteger atomicIntegerRowCount = new AtomicInteger(rowIndex);
        Collection collection = null;
        if (infoList instanceof List<?>) {
            collection = (List) infoList;
        } else if (infoList instanceof Set<?>) {
            collection = (Set) infoList;
        }
        Optional.ofNullable((Collection) infoList).get().forEach(info -> {
            AtomicInteger atomicIntegerCellCount = new AtomicInteger(cellIndex);
            //-1 is for remove empty row
            XSSFRow row = spreadsheet.createRow(rowIndex.getAndIncrement());
            if (info instanceof ProjectSizeInfo) {
                ProjectSizeInfo projectSizeInfo = (ProjectSizeInfo) info;
                Cell cell = row.createCell(atomicIntegerCellCount.getAndIncrement());
                setCellvalueInExcel(projectSizeInfo.getTechName(), cell, false, null);
                Cell secondCell = row.createCell(atomicIntegerCellCount.getAndIncrement());
                setCellvalueInExcel(projectSizeInfo.getNoOfFiles(), secondCell, false, null);
                Cell thirdCell = row.createCell(atomicIntegerCellCount.getAndIncrement());
                setCellvalueInExcel(projectSizeInfo.getNoOfLines(), thirdCell, false, null);
                Cell fourthCell = row.createCell(atomicIntegerCellCount.getAndIncrement());
                setCellvalueInExcel(projectSizeInfo.getNoOfMethods(), fourthCell, false, null);
            } else {
                Cell cell = row.createCell(atomicIntegerCellCount.getAndIncrement());
                setCellvalueInExcel(info, cell, false, null);
            }
        });
    }

    private long noOfLines = 0;

    private String getApplicationSize(String techStackType, List<ProjectSizeInfo> projectSizeInfos) {
        if (Optional.ofNullable(projectSizeInfos).isEmpty() || Optional.ofNullable(projectSizeInfos.isEmpty()).get().booleanValue()) {
            return techStackType;
        }
        long small = Long.parseLong(generatePropertyFileMap().get(String.valueOf(DependencyEnum.small)));
        long large = Long.parseLong(generatePropertyFileMap().get(String.valueOf(DependencyEnum.large)));
        projectSizeInfos.forEach(projectSizeInfo -> {
            noOfLines = noOfLines + projectSizeInfo.getNoOfLines();
        });
        if (noOfLines <= small) {
            noOfLines = 0;
            return techStackType + DependencyFinderConstant.BLANK_SPACE + DependencyFinderConstant.OPEN_ROUND_BRACKET
                    + DependencyFinderConstant.SMALL +
                    DependencyFinderConstant.CLOSE_ROUND_BRACKET;
        } else if (noOfLines > small && noOfLines < large) {
            noOfLines = 0;
            return techStackType + DependencyFinderConstant.BLANK_SPACE + DependencyFinderConstant.OPEN_ROUND_BRACKET
                    + DependencyFinderConstant.MEDIUM +
                    DependencyFinderConstant.CLOSE_ROUND_BRACKET;
        } else if (noOfLines >= large) {
            noOfLines = 0;
            return techStackType + DependencyFinderConstant.BLANK_SPACE + DependencyFinderConstant.OPEN_ROUND_BRACKET
                    + DependencyFinderConstant.LARGE +
                    DependencyFinderConstant.CLOSE_ROUND_BRACKET;
        }
        return techStackType;
    }

    private <K, V> void populateProjectDetails(Map<K, V> map, XSSFSheet spreadsheet, AtomicInteger atomicIntegerRowCount, int cellCountIndex,
                                               boolean style, XSSFWorkbook workbook) {
        Optional.ofNullable(map).get().forEach((type, file) -> {
            if (spreadsheet.getSheetName().equalsIgnoreCase(DependencyFinderConstant.KNOWN_TECHNOLOGY_INFO) && type instanceof String &&
                    ((String) type).equalsIgnoreCase(String.valueOf(DependencyEnum.Unknown_Technology))) {

            } else if (spreadsheet.getSheetName().equalsIgnoreCase(DependencyFinderConstant.UNKNOWN_TECHNOLOGY_INFO) && type instanceof String &&
                    ((String) type).equalsIgnoreCase(String.valueOf(DependencyEnum.Known_Technology))) {

            } else {
                int rowCount = atomicIntegerRowCount.getAndIncrement();
                XSSFRow row = spreadsheet.createRow(rowCount);
                AtomicInteger atomicIntegerCellCount = new AtomicInteger(cellCountIndex);
                if (style && type instanceof String && file instanceof List<?>) {
                    type = (K) getApplicationSize((String) type, (List<ProjectSizeInfo>) file);
                }
                if (!spreadsheet.getSheetName().equalsIgnoreCase(DependencyFinderConstant.UNKNOWN_TECHNOLOGY_INFO) &&
                        !spreadsheet.getSheetName().equalsIgnoreCase(DependencyFinderConstant.KNOWN_TECHNOLOGY_INFO)) {
                    Cell cell = row.createCell(atomicIntegerCellCount.getAndIncrement());
                    this.setCellvalueInExcel(type, cell, style, workbook);
                }
                if (file instanceof List<?>) {
                    if (!spreadsheet.getSheetName().equalsIgnoreCase(DependencyFinderConstant.UNKNOWN_TECHNOLOGY_INFO) &&
                            !spreadsheet.getSheetName().equalsIgnoreCase(DependencyFinderConstant.KNOWN_TECHNOLOGY_INFO)) {
                        setCellvalueInExcel(((List<?>) file).size(), row.createCell(atomicIntegerCellCount.getAndIncrement()), false, workbook);
                    }
                    populateListInfo(atomicIntegerRowCount, atomicIntegerCellCount.getAndIncrement(), spreadsheet, (List<?>) file);
                } else if (file instanceof Set<?>) {
                    setCellvalueInExcel(((Set<?>) file).size(), row.createCell(atomicIntegerCellCount.getAndIncrement()), style, workbook);
                    populateListInfo(atomicIntegerRowCount, atomicIntegerCellCount.getAndIncrement(), spreadsheet, (Set<String>) file);
                } else if (file instanceof Map) {
                    populateProjectDetails((Map<K, V>) file, spreadsheet, atomicIntegerRowCount, atomicIntegerCellCount.getAndIncrement(), style, workbook);
                } else if (file instanceof ProjectCommonInfo) {
                    ProjectCommonInfo commonInfo = (ProjectCommonInfo) file;
                    if (Optional.ofNullable(commonInfo.getModuleName()).isPresent()) {
                        setCellvalueInExcel(commonInfo.getModuleName(), row.createCell(atomicIntegerCellCount.getAndIncrement()), style, workbook);
                    } else if (Optional.ofNullable(commonInfo.getApplicationPackagingType()).isPresent()) {
                        setCellvalueInExcel(Optional.ofNullable(commonInfo.getApplicationPackagingType()).get().size(), row.createCell(atomicIntegerCellCount.getAndIncrement()), style, workbook);
                    }

                    Map<K, V> commonInfomap = Optional.ofNullable(commonInfo.getDependencyType()).isPresent() ?
                            (Map<K, V>) commonInfo.getDependencyType() : Optional.ofNullable(commonInfo.getApplicationPackagingType()).isPresent() ?
                            (Map<K, V>) commonInfo.getApplicationPackagingType() : (Map<K, V>) commonInfo.getTechnologyCount();

                    if (Optional.ofNullable(commonInfomap).isPresent()) {
                        populateProjectDetails(commonInfomap, spreadsheet, atomicIntegerRowCount, atomicIntegerCellCount.getAndIncrement(), false, workbook);
                    }

                } else {
                    Cell secondCell = row.createCell(atomicIntegerCellCount.getAndIncrement());
                    this.setCellvalueInExcel(file, secondCell, style, workbook);
                }
            }
        });
    }

    private <T> void setCellvalueInExcel(T value, Cell cell, boolean style, XSSFWorkbook workbook) {
        if (style) {
            // start - for alignment
            Font boldFont = workbook.createFont();
            boldFont.setBold(true);
            boldFont.setColor(HSSFColor.HSSFColorPredefined.DARK_RED.getIndex());
            boldFont.setFontHeightInPoints((short) 15);
            CellStyle boldStyle = workbook.createCellStyle();
            boldStyle.setFont(boldFont);
            boldStyle.setAlignment(HorizontalAlignment.CENTER);
            boldStyle.setFillForegroundColor(IndexedColors.SKY_BLUE.getIndex());
            boldStyle.setFillPattern(FillPatternType.FINE_DOTS);
            // end - for alignment
            cell.setCellStyle(boldStyle);
            cell.setCellValue(value.toString());
            return;
        }
        if (value instanceof Integer) {
            cell.setCellValue((int) value);
        } else if (value instanceof Long) {
            cell.setCellValue((long) value);
        } else {
            cell.setCellValue(value.toString());
        }

    }

    public void createAndWriteInToExcelFile(XSSFWorkbook workbook, String excelPath) {
        logger.info("Output file : " + excelPath);
        try {
            FileOutputStream out = new FileOutputStream(
                    new File(excelPath));
            XSSFFormulaEvaluator.evaluateAllFormulaCells(workbook);
            workbook.write(out);
            out.close();
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public Map generateGradlePropertyFileMap(String path) {
        Map<String, String> gradleProperty = new ConcurrentHashMap<>();
        //logger.info("gradle Property file path " + path);
        Properties appProps = new Properties();
        try {
            appProps.load(new FileInputStream(path));
        } catch (IOException e) {
            logger.error("File not present : " + path);
            //throw new RuntimeException(e);
        }
        appProps.forEach((key, val) -> {
            gradleProperty.put((String) key, (String) val);
        });
        return gradleProperty;
    }

    public Map<String, String> generatePropertyFileMap() {
        if (!getPropertyFileMap().isEmpty()) {
            return getPropertyFileMap();
        }

        String propertyFile = Thread.currentThread().getContextClassLoader().getResource(DependencyFinderConstant.APPLICATION_PROPERTY).getPath();
        logger.info("Property file path " + propertyFile);
        Properties appProps = new Properties();
        try {
            appProps.load(new FileInputStream(propertyFile));
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        appProps.forEach((key, val) -> {
            getPropertyFileMap().put((String) key, (String) val);
        });
        return getPropertyFileMap();
    }

    public void fetchGradleProjectInfo(String projectPaths, String gradleHomePath, String gradleDistributionUri) {
        String appBuildTool = commonUtils.getBuildTool(projectPaths);
        logger.info("Application Build Tool: " + appBuildTool + " for project path " + projectPaths);
        Optional.ofNullable("GRADLE".equalsIgnoreCase(appBuildTool)).filter(Boolean::booleanValue).ifPresent(isGradle -> {
            commonUtils.upgradeAndFetchGradleProjectInfo(projectPaths, gradleHomePath, gradleDistributionUri);
        });
    }
    public void readFile(String path) {
        if (Optional.ofNullable(path).isEmpty() || path.isEmpty()) {
            logger.info("************File path is empty***********");
            return;
        }
        //Read value from property file
        //generatePropertyFileMap();
        Arrays.stream(Optional.of(generatePropertyFileMap().get(String.valueOf(DependencyEnum.internal)))
                .get().split(",")).forEach(v -> internalList.add(v));

        Arrays.stream(Optional.of(generatePropertyFileMap().get(String.valueOf(DependencyEnum.external)))
                .get().split(",")).forEach(v -> externalList.add(v));
        //Creating teck stack empty object
        Arrays.stream(Optional.of(generatePropertyFileMap().get(String.valueOf(DependencyEnum.techStackHeader))).get()
                .split(",")).forEach(v -> {
            List<ProjectSizeInfo> projectSizeInfos = new ArrayList<>();
            getProjectInfo().getTeckStackInfo().put(v, projectSizeInfos);
        });
        //initializing gradle project info
        fetchGradleProjectInfo(path, generatePropertyFileMap().get("gradleHome"), generatePropertyFileMap().get("gradleDistributionUri"));
        readContentFromFile(new File(path));

        // display the result start here
        /*System.out.println("file size : " + getProjectInfo().getFileCount());
        System.out.println("################# Application Build Info ##############");
        getProjectInfo().getApplicationType().forEach((k, v) -> {
            System.out.println(k + " ->");
            v.forEach(System.out::println);
        });
        System.out.println("################# Dependency ##############");
        getProjectInfo().getDependencyMap().forEach((k, v) -> {
            System.out.println(k);
            v.forEach((ke, va) -> {
                System.out.println(ke);
                va.forEach(System.out::println);
            });
        });
        System.out.println("################# Data Base details ##############");
        getProjectInfo().getDbDetails().forEach((k, v) -> {
            System.out.println("Module Name: " + k);
            v.forEach((ke, va) -> {
                System.out.println("Data base name: " + ke);
                va.forEach(System.out::println);
            });
        });
        System.out.println("################# Project details Info ##############");
        getProjectInfo().getDetailedProjectInfo().stream().forEach(getKnowYourProject()::displayProjectInfo);
        System.out.println("################# Web Service Info ##############");
        getProjectInfo().getWebServiceMap().forEach((k, v) -> {
            System.out.println("Webservice module name : " + k);
            v.forEach((type, endpoint) -> {
                System.out.println("Type : " + type);
                endpoint.forEach(System.out::println);
            });
        });*/
        // display the result end here
    }

    public void readContentFromFile(File dir) {
        File[] filesDirs = dir.listFiles();
        Arrays.stream(filesDirs).forEach(this::performOperationOnFile);
    }

    public void performOperationOnFile(File file) {
        if (Optional.ofNullable(file).isEmpty()) {
            return;
        }
        if (file.isDirectory()) {
            readContentFromFile(file);
        }
        findBuildToolWithDBDInfo(file);
        populateFileInfo(file);
        checkCacheDependencyInProject(file);
    }

    public void findBuildToolWithDBDInfo(File file) {
        if (Optional.of(file.getName().contains(DependencyFinderConstant.JDBC_XML)).get().booleanValue()) {
            getProjectInfo().getDbDetails().put(file.getPath(), new ConcurrentHashMap<>());
            populateDBDetails(file);
        } else if (Optional.of(file.getName().equalsIgnoreCase(DependencyFinderConstant.BUILD_GRADLE)).get().booleanValue()) {
            antBuildProject = false;
            Map<String, String> gradlePropMap = generateGradlePropertyFileMap(file.getParent() + "\\gradle.properties");
            generateDependencyMap(file);
            this.getProjectInfo().setApplicationBuildType(file.getName());
            //ProjectSizeInfo projectSizeInfo = performTechnologyCount(file, DependencyFinderConstant.GRADLE, true);
            findGradleApplicationType(file, gradlePropMap);
        } else if (Optional.of(file.getName().equalsIgnoreCase(DependencyFinderConstant.POM_XML)).get().booleanValue()) {
            antBuildProject = false;
            generateDependencyMap(file);
            this.getProjectInfo().setApplicationBuildType(file.getName());
            Map<String, String> propMap = createPropMapFromXMLFile(file);
            //ProjectSizeInfo projectSizeInfo = performTechnologyCount(file, DependencyFinderConstant.MAVEN, true);
            findPOMApplicationType(file, propMap);
        }
    }

    public void generateDependencyMap(File file) {
        ProjectCommonInfo commonInfo = new ProjectCommonInfo();
        commonInfo.setModuleName("");
        Map<String, List<String>> depMap = new ConcurrentHashMap<>();
        commonInfo.setDependencyType(depMap);
        depMap.put(String.valueOf(DependencyEnum.internal), new ArrayList<String>());
        depMap.put(String.valueOf(DependencyEnum.external), new ArrayList<String>());
        depMap.put(String.valueOf(DependencyEnum.Miscellaneous), new ArrayList<String>());
        getProjectInfo().getDependencyMap().put(file.getPath(), commonInfo);
    }

    private boolean gradleDependencyFound = false;
    private boolean gradleArtifactFound = false;
    private boolean isArtifactIdFound = false;
    private boolean isArtifactFound = false;

    public void fetchGradleDependency(File file) {
        Optional.ofNullable(commonUtils.getGradleProjectInfo()).ifPresent(gradleProjectInfoDTO -> {
            logger.info("Gradle Project Info : " + gradleProjectInfoDTO.getGradleDependencies().get(file.getPath()));
            Optional.ofNullable(gradleProjectInfoDTO.getGradleDependencies().get(file.getPath().toLowerCase())).ifPresent(moduleDependencieMap -> {
                moduleDependencieMap.forEach((moduleName, dependencyList) -> {
                    dependencyList.forEach(dependency -> {
                        String version = Optional.ofNullable(dependency.getVersion()).orElse("");
                        String artifactId = Optional.ofNullable(dependency.getArtifactId()).orElse("");
                        String groupId = Optional.ofNullable(dependency.getGroupId()).orElse("");
                        Optional.ofNullable(StringUtils.isNotEmpty(version) && StringUtils.isNotEmpty(artifactId) &&
                                StringUtils.isNotEmpty(groupId)).filter(Boolean::booleanValue).ifPresent(isPresent -> {
                            StringBuilder builder = new StringBuilder();
                            builder.append("groupId: ").append(groupId).append(" artifactId: ").append(artifactId).append(" version: ").append(version).append("\n");
                            getProjectInfo().getDependencyMap().get(file.getPath()).setModuleName(moduleName);
                            extractDependency(groupId, builder, file);
                        });
                    });
                });
            });
        });
    }
    public void fetchGradlePackagingInfo(File file) {
        Optional.ofNullable(commonUtils.getGradleProjectInfo()).ifPresent(gradleProjectInfoDTO -> {
            Optional.ofNullable(gradleProjectInfoDTO.getGradlePackagingInfos()).ifPresent(packagingInfo -> {
                Optional.ofNullable(packagingInfo.containsKey(file.getPath())).filter(Boolean::booleanValue).ifPresent(isPresent -> {
                    packagingInfo.get(file.getPath().toLowerCase()).forEach((gradleArtifactDetails) -> {
                        populatePackagingInfo(file, gradleArtifactDetails.getArtifactId(), gradleArtifactDetails.getPackagingType());
                    });
                });
            });
        });
    }
    public void findGradleApplicationType(File file, Map<String, String> gradlePropMap) {
        boolean disableGradleToolingApi = Boolean.parseBoolean(generatePropertyFileMap().get("disableGradleToolingApi"));
        if (!disableGradleToolingApi) {
            fetchGradleDependency(file);
            fetchGradlePackagingInfo(file);
            return;
        }
        try (BufferedReader br = Files.newBufferedReader(Paths.get(file.getPath()))) {
            br.lines().forEach(e -> {
                //projectSizeInfo.setNoOfLines(projectSizeInfo.getNoOfLines()+1);
                if (e.contains("publishing") && e.contains("{")) {
                    gradleArtifactFound = true;
                } else if (gradleArtifactFound && e.contains("artifactId")) {
                    isArtifactIdFound = true;
                    Arrays.stream(e.trim().split("artifactId")).forEach(sub -> {
                        if (StringUtil.isNotBlank(Optional.ofNullable(sub).get())) {
                            getProjectInfo().getDependencyMap().get(file.getPath()).setModuleName(Optional.ofNullable(gradlePropMap.get(sub.trim())).isPresent() ?
                                    gradlePropMap.get(sub.trim()) : sub.trim());
                            this.setArtifactId(getProjectInfo().getDependencyMap().get(file.getPath()).getModuleName());
                        }
                    });
                } else if (gradleArtifactFound && e.contains("artifact")) {
                    isArtifactFound = true;
                    Arrays.stream(e.trim().split("artifact")).forEach(buildType -> {
                        if (StringUtil.isNotBlank(Optional.ofNullable(buildType).get())) {
                            Map<String, ProjectCommonInfo> applicationTypeMap = getProjectInfo().getApplicationType();
                            if (Optional.ofNullable(applicationTypeMap.get(buildType.trim())).isPresent()) {
                                applicationTypeMap.get(buildType.trim()).getApplicationPackagingType().put(file.getPath(), getArtifactId());
                            } else {
                                ProjectCommonInfo commonInfo = new ProjectCommonInfo();
                                Map<String, String> packagingmap = new ConcurrentHashMap<>();
                                commonInfo.setApplicationPackagingType(packagingmap);
                                packagingmap.put(file.getPath(), getArtifactId());
                                applicationTypeMap.put(buildType.trim(), commonInfo);
                            }
                        }
                    });

                }
                //reset the value
                if (isArtifactIdFound && isArtifactFound) {
                    gradleArtifactFound = false;
                    isArtifactIdFound = false;
                    isArtifactFound = false;
                }
                if (e.contains(DependencyFinderConstant.DEPENDENCIES) && !e.contains(DependencyFinderConstant.DOUBLE_SLASH)) {
                    gradleDependencyFound = true;
                } else if (e.trim().equalsIgnoreCase(DependencyFinderConstant.CLOSE_BRACKET) && !e.contains(DependencyFinderConstant.DOUBLE_SLASH)) {
                    gradleDependencyFound = false;
                } else if (gradleDependencyFound && !e.contains(DependencyFinderConstant.DOUBLE_SLASH)) {
                    if (internalList.stream().anyMatch(m -> e.trim().toLowerCase().contains(m.trim().toLowerCase()))) {
                        isInternal = true;
                    } else if (externalList.stream().anyMatch(m -> e.trim().toLowerCase().contains(m.trim().toLowerCase()))) {
                        isExternal = true;
                    }
                    segregateDependency(file, e);
                }

            });
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private boolean dependencyFla = false;
    private boolean isInternal = false;
    private boolean isExternal = false;
    private StringBuilder builder = null;
    private int dependency_count = 0;

    public String getArtifactId() {
        return artifactId;
    }

    public void setArtifactId(String artifactId) {
        this.artifactId = artifactId;
    }

    private String artifactId;
    private UtilServiceImpl utilService = new UtilServiceImpl();
    private MavenUtils mavenUtils = new MavenUtils();
    private CommonUtils commonUtils = new CommonUtils();

    public void fetchMavenDependency(File file, PackagingInfo packagingInfo) {
        List<Dependency> dependencyList = null;
        try {
            dependencyList = utilService.getPomDependencies(file.getPath());
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
        if (Optional.ofNullable(dependencyList).isEmpty()) {
            return;
        }
        getProjectInfo().getDependencyMap().get(file.getPath()).setModuleName(packagingInfo.getArtifactName());
        Map<String, String> propMap = mavenUtils.getMavenPropertyValues(file, commonUtils);
        Optional.of(dependencyList).ifPresentOrElse(dependencies -> {
            dependencies.forEach(dependency -> {
                String version = mavenUtils.getVersionVal(Optional.ofNullable(dependency.getVersion()).orElse(""), propMap);
                String artifactId = Optional.ofNullable(dependency.getArtifactId()).orElse("");
                String groupId = Optional.ofNullable(dependency.getGroupId()).orElse("");
                StringBuilder builder = new StringBuilder();
                builder.append("groupId: ").append(groupId).append(" artifactId: ").append(artifactId).append(" version: ").append(version).append("\n");
                extractDependency(groupId, builder, file);
            });
        }, () -> {
            logger.info("No artifact details are found");
            return;
        });
    }

    public void extractDependency(String groupId, StringBuilder builder, File file) {
        if (internalList.stream().anyMatch(m -> groupId.trim().toLowerCase().contains(m.trim().toLowerCase()))) {
            segregateDependency(file, builder.toString(), String.valueOf(DependencyEnum.internal));
        } else if (externalList.stream().anyMatch(m -> groupId.trim().toLowerCase().contains(m.trim().toLowerCase()))) {
            segregateDependency(file, builder.toString(), String.valueOf(DependencyEnum.external));
        } else {
            segregateDependency(file, builder.toString(), String.valueOf(DependencyEnum.Miscellaneous));
        }
    }
    public void fetchPackagingInfo(File file , PackagingInfo packagingInfo) {
        populatePackagingInfo(file, packagingInfo.getArtifactName(), packagingInfo.getPackagingType());
    }
    public void segregateDependency(File file, String line, String dependencyType) {
        getProjectInfo().getDependencyMap().get(file.getPath()).getDependencyType().get(dependencyType).add(line);
    }

    public void populatePackagingInfo(File file, String artifactId, String buildType) {
        Optional.ofNullable(StringUtils.isEmpty(artifactId) && StringUtils.isEmpty(buildType)).filter(Boolean::booleanValue).ifPresentOrElse(flag -> {
            logger.info("ArtifactId and buildType is empty");
        }, () -> {
            Map<String, ProjectCommonInfo> applicationTypeMap = getProjectInfo().getApplicationType();
            //logger.info("buildType : " + buildType + " file path " + file.getPath() + " artifactId : " + artifactId);
            Optional.ofNullable(applicationTypeMap.get(buildType.trim())).ifPresentOrElse(commonInfo -> {
                commonInfo.getApplicationPackagingType().put(file.getPath(), artifactId);
            }, () -> {
                ProjectCommonInfo commonInfo = new ProjectCommonInfo();
                Map<String, String> packagingmap = new ConcurrentHashMap<>();
                commonInfo.setApplicationPackagingType(packagingmap);
                packagingmap.put(file.getPath(), artifactId);
                applicationTypeMap.put(buildType.trim(), commonInfo);
            });
        });
    }
    public void findPOMApplicationType(File file, Map<String, String> propMap) {
        boolean disableMavenApi = Boolean.parseBoolean(generatePropertyFileMap().get("disableMavenApi"));
        if (!disableMavenApi) {
            PackagingInfo packagingInfo = mavenUtils.getPOMPackagingInfo(file);
            fetchMavenDependency(file, packagingInfo);
            fetchPackagingInfo(file, packagingInfo);
            return;
        }
        try (BufferedReader br = Files.newBufferedReader(Paths.get(file.getPath()))) {
            br.lines().forEach(e -> {
                if (e.trim().contains(DependencyFinderConstant.artifactId)) {
                    Arrays.stream(e.split(DependencyFinderConstant.artifactId)).forEach(artifactName -> {
                        if (artifactName.contains("</")) {
                            String artifactId = artifactName.substring(0, artifactName.indexOf('<'));
                            setArtifactId(artifactId);
                        }
                    });

                }

                if (e.contains(DependencyFinderConstant.PACKAGING_TAG)) {
                    //setting the ArtifactId for dependency
                    Optional.ofNullable(getProjectInfo().getDependencyMap().get(file.getPath())).get().setModuleName(getArtifactId());
                    Arrays.stream(e.split(DependencyFinderConstant.PACKAGING_TAG)).forEach(f -> {
                        if (f.contains("</")) {
                            String buildType = f.substring(0, f.indexOf('<'));
                            populatePackagingInfo(file, getArtifactId(), buildType);
                        }
                    });
                }
                if (e.contains(DependencyFinderConstant.DEPENDENCY_TAG)) {
                    builder = getStringBuilder(true);
                    dependencyFla = true;
                    builder.append(e).append("\n");
                } else if (e.contains(DependencyFinderConstant.DEPENDENCY_CLOSE_TAG)) {
                    dependency_count = 0;
                    dependencyFla = false;
                    builder.append(e).append("\n");
                    segregateDependency(file, null);
                }
                if (dependencyFla && dependency_count < 3 && (e.contains("<groupId>") || e.contains("<artifactId>") || e.contains("<version>"))) {
                    dependency_count++;
                    builder.append(getVersionValue(e, propMap)).append("\n");
                    if (e.contains("<groupId>")) {
                        if (internalList.stream().anyMatch(m -> e.trim().toLowerCase().contains(m.trim().toLowerCase()))) {
                            isInternal = true;
                        } else if (externalList.stream().anyMatch(m -> e.trim().toLowerCase().contains(m.trim().toLowerCase()))) {
                            isExternal = true;
                        }
                    }
                }
            });
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public String getVersionValue(String line, Map<String, String> propMap) {
        if (line.contains("<version>") && line.contains("{")) {
            String versionProp = line.trim().substring(line.trim().indexOf('{') + 1, line.trim().indexOf('}'));
            return "<version>" + propMap.get(versionProp) + "</version>";
        }
        return line;
    }

    private Document getDocumentObject(File file) {
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
        DocumentBuilder db = null;
        Document doc = null;
        try {
            db = dbf.newDocumentBuilder();
            doc = db.parse(file);
        } catch (ParserConfigurationException | SAXException | IOException e) {
            // throw new RuntimeException(e);
        }
        return doc;
    }

    public Map<String, String> createPropMapFromXMLFile(File file) {
        Map<String, String> propMap = new HashMap<>();
        Document doc = getDocumentObject(file);
        NodeList nodeList = doc.getElementsByTagName("properties");
        IntStream.range(0, nodeList.getLength()).forEach(prop -> {
            Node node = nodeList.item(prop);
            Element eElement = (Element) node;
            NodeList childNodeList = eElement.getChildNodes();
            IntStream.range(0, childNodeList.getLength()).forEach(childProp -> {
                Node childNode = childNodeList.item(childProp);
                if (childNode.getNodeType() == Node.ELEMENT_NODE) {
                    propMap.put(childNode.getNodeName().trim(), childNode.getTextContent().trim());
                }
            });
        });
        return propMap;
    }

    boolean isContainPropertyflag = false;

    public void populateDBDetails(File file) {

        try (BufferedReader br = Files.newBufferedReader(Paths.get(file.getPath()))) {

            br.lines().forEach(e -> {
                StringBuilder builder;
                boolean eod = Optional.of(e.contains("/>")).get().booleanValue();
                if (Optional.of(e.contains("<properties.")).get().booleanValue() && eod) {
                    addDBDetailInToList(e, file);
                } else if (Optional.of(e.contains("<properties.")).get().booleanValue() && !eod) {
                    builder = new StringBuilder();
                    builder.append(e);
                    addDBDetailInToList(e, file);
                    isContainPropertyflag = true;
                } else if (isContainPropertyflag && !eod) {
                    removeAndAddDBDetails(e, file);
                } else if (isContainPropertyflag && eod) {
                    removeAndAddDBDetails(e, file);
                    //getProjectInfo().getDbDetails().get(file.getPath()).get(getDbName()).add(e);
                    isContainPropertyflag = false;
                }
            });
            //Remove empty Map which does not contain any DB details.
            boolean flag = Optional.ofNullable(getProjectInfo().getDbDetails().get(file.getPath())).get().isEmpty() ?
                    removeEmptyDBDetails(true, file) : removeEmptyDBDetails(false, file);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private void removeAndAddDBDetails(String dbMsg, File fileObj) {
        List<String> dbList = getProjectInfo().getDbDetails().get(fileObj.getPath()).get(getDbName());
        String lastValue = dbList.get(dbList.size() - 1);
        dbList.remove(dbList.size() - 1);
        dbList.add(lastValue.trim() + " " + dbMsg.trim());
    }

    public void addDBDetailInToList(String e, File file) {
        setDbName(Optional.of(e.substring(e.indexOf("<properties.") + ("<properties.").length(), e.length())).get().split(" ")[0].trim());
        List<String> dbDetailsList = Optional.ofNullable(getProjectInfo().getDbDetails().get(file.getPath()).get(getDbName())).isPresent() ?
                getProjectInfo().getDbDetails().get(file.getPath()).get(getDbName()) : new ArrayList<>();
        dbDetailsList.add(e);
        getProjectInfo().getDbDetails().get(file.getPath()).put(getDbName(), dbDetailsList);
    }

    public boolean removeEmptyDBDetails(boolean isEmpty, File file) {
        if (isEmpty) {
            Optional.ofNullable(getProjectInfo().getDbDetails()).get().remove(file.getPath());
            return true;
        }
        return false;
    }

    public void segregateDependency(File file, String line) {
        if (isInternal) {
            isInternal = false;

            getProjectInfo().getDependencyMap().get(file.getPath()).getDependencyType().get(String.valueOf(DependencyEnum.internal))
                    .add(Optional.ofNullable(line).isPresent() ? line : builder.toString());
        } else if (isExternal) {
            isExternal = false;
            getProjectInfo().getDependencyMap().get(file.getPath()).getDependencyType().get(String.valueOf(DependencyEnum.external))
                    .add(Optional.ofNullable(line).isPresent() ? line : builder.toString());
        } else {
            getProjectInfo().getDependencyMap().get(file.getPath()).getDependencyType().get(String.valueOf(DependencyEnum.Miscellaneous))
                    .add(Optional.ofNullable(line).isPresent() ? line : builder.toString());
        }
    }

    public StringBuilder getStringBuilder(boolean flag) {
        if (flag) {
            builder = new StringBuilder();
            return builder;
        } else {
            return builder;
        }
    }

    public List<ProjectSizeInfo> getProjectSizeInfo(String knownTechnology, String fileExt, boolean flag) {
        if (flag) {
            return getProjectInfo().getTeckStackInfo().get(String.valueOf(DependencyEnum.Others));
        }
        if (knownTechnology.toLowerCase().contains(fileExt.toLowerCase())) {
            return getProjectInfo().getTeckStackInfo().get(String.valueOf(DependencyEnum.Known_Technology));
        } else {
            return getProjectInfo().getTeckStackInfo().get(String.valueOf(DependencyEnum.Unknown_Technology));
        }
    }

    public ProjectSizeInfo performTechnologyCount(File file, String fileExt, boolean flag) {
        String knownTechnology = generatePropertyFileMap().get(String.valueOf(DependencyEnum.Known_Technology));
        List<ProjectSizeInfo> projectSizeInfos = getProjectSizeInfo(knownTechnology, fileExt, flag);

        if (Optional.ofNullable(projectSizeInfos).isEmpty()) {
            return null;
        }
        if (projectSizeInfos.isEmpty()) {
            ProjectSizeInfo projectSizeInfo = new ProjectSizeInfo();
            projectSizeInfo.setNoOfFiles(1);
            projectSizeInfo.setTechName(fileExt);
            projectSizeInfos.add(projectSizeInfo);
            return projectSizeInfo;
        }

        List<ProjectSizeInfo> list = projectSizeInfos.stream().filter(projectSizeInfo ->
                projectSizeInfo.getTechName().equalsIgnoreCase(fileExt)).collect(Collectors.toList());
        if (Optional.ofNullable(list).isPresent() && !list.isEmpty()) {
            ProjectSizeInfo projectSizeInfo = list.get(0);
            projectSizeInfo.setNoOfFiles(projectSizeInfo.getNoOfFiles() + 1);
            return projectSizeInfo;
        } else {
            ProjectSizeInfo projectSizeInfo = new ProjectSizeInfo();
            projectSizeInfo.setNoOfFiles(1);
            projectSizeInfo.setTechName(fileExt);
            projectSizeInfos.add(projectSizeInfo);
            return projectSizeInfo;
        }
    }

    public void populateFileInfo(File file) {
        if (Optional.ofNullable(file.getName().contains(".xml")).get().booleanValue()) {
            getRPCCallFromXML(file);
        }
        if ((file.getName().contains(".") && file.isFile() && !Arrays.stream(Optional.of(generatePropertyFileMap().get(String.valueOf(DependencyEnum.ignoreList))).get()
                .split("#")).anyMatch(e -> file.getName().substring(file.getName().lastIndexOf('.')).trim().equalsIgnoreCase(e.trim())))
                && !file.getName().substring(file.getName().lastIndexOf('.') + 1).matches(".*\\d.*")) {
            String fileExt = file.getName().substring(file.getName().lastIndexOf('.') + 1);
            ProjectSizeInfo projectSizeInfo = performTechnologyCount(file, fileExt, false);
            DetailedProjectInfo info = new DetailedProjectInfo();
            info.setFileName(file.getPath());
            info.setFileExt(fileExt);
            getProjectInfo().getDetailedProjectInfo().add(info);
            try {
                List<String> lines = Files.readAllLines(Paths.get(file.getPath()), Charset.defaultCharset());
                this.file = file;
                lines.forEach(this::readLineByLine);
            } catch (IOException e) {
                logger.error("file name : " + file.getPath());
                //e.printStackTrace();
            }
            projectSizeInfo.setNoOfLines(projectSizeInfo.getNoOfLines() + info.getNoOfLine());
            projectSizeInfo.setNoOfMethods(projectSizeInfo.getNoOfMethods() + info.getNoOfMethod());
        }
    }

    public void findWebService(String line, File file) {
        findRestWebService(line, file);
        findRPCCall(line, file);
    }

    public void findRPCCall(String line, File file) {
        if (Optional.ofNullable(line.contains("extends")).get().booleanValue() &&
                Optional.ofNullable(line.contains("EJBObject")).get().booleanValue() &&
                Optional.ofNullable(line.contains("{")).get().booleanValue()) {

            createRPCMap(line, file);
        } else if (Optional.ofNullable(file.getName().contains(".xml")).get().booleanValue()) {
            //getRPCCallFromXML(line,file);
        }
    }

    private void createRPCMap(String line, File file) {
        if (Optional.of(getProjectInfo().getWebServiceMap()).get().containsKey(file.getPath())) {
            if (Optional.of(getProjectInfo().getWebServiceMap().get(file.getPath())).get().containsKey(generatePropertyFileMap().get(String.valueOf(DependencyEnum.RPC)))) {
                getProjectInfo().getWebServiceMap().get(file.getPath()).get(generatePropertyFileMap().get(String.valueOf(DependencyEnum.RPC))).add(line);
            } else {
                Map<String, Set<String>> rpcCallMap = new ConcurrentHashMap<>();
                Set<String> rpcCall = new HashSet<>();
                rpcCall.add(line);
                getProjectInfo().getWebServiceMap().get(file.getPath()).put(generatePropertyFileMap().get(String.valueOf(DependencyEnum.RPC)), rpcCall);
            }

        } else {
            Map<String, Set<String>> rpcCallMap = new ConcurrentHashMap<>();
            Set<String> rpcCall = new HashSet<>();
            rpcCall.add(line);
            rpcCallMap.put(generatePropertyFileMap().get(String.valueOf(DependencyEnum.RPC)), rpcCall);
            getProjectInfo().getWebServiceMap().put(file.getPath(), rpcCallMap);
        }
    }

    private void getRPCCallFromXML(File file) {
        Document doc = getDocumentObject(file);
        if (Optional.ofNullable(doc).isEmpty()) {
            return;
        }
        NodeList nodeList = doc.getElementsByTagName("session");
        IntStream.range(0, nodeList.getLength()).forEach(tag -> {
            Node node = nodeList.item(tag);
            if (node.getNodeType() == Node.ELEMENT_NODE) {
                Element eElement = (Element) node;
                String sessionType = eElement.getElementsByTagName("session-type").item(0).getTextContent();
                String ejbClass = eElement.getElementsByTagName("ejb-class").item(0).getTextContent();
                createRPCMap(ejbClass, file);
            }
        });
    }

    public void findRestWebService(String line, File file) {
        if (isContainEndPoint(line)
                && !Arrays.stream(Optional.of(generatePropertyFileMap().get(String.valueOf(DependencyEnum.webServiceURLIgnoreList))).get()
                .split("#")).anyMatch(e -> line.trim().contains(e.trim()))
                && !Arrays.stream(Optional.of(generatePropertyFileMap().get(String.valueOf(DependencyEnum.webServiceModulePathIgnoreList))).get()
                .split("#")).anyMatch(e -> file.getPath().contains(e.trim()))) {
            int lastIndex = getLastIndex(line);
            //https: will always contain // hence create substring and check is line commented if yes then return.
            if (lastIndex > 0 && (line.substring(0, lastIndex).contains("//") || line.substring(0, lastIndex).contains("*")
                    || line.substring(0, lastIndex).contains("#"))) {
                return;
            }
            String finalEndPoint = returnURLFromXMLFile(line, file);
            String key = file.getPath();
            boolean isHttpEndPoint = Optional.ofNullable(finalEndPoint.contains(generatePropertyFileMap().get(String.valueOf(DependencyEnum.unSecureURL)))).get().booleanValue();
            if (Optional.of(getProjectInfo().getWebServiceMap()).get().containsKey(key)) {
                if (isHttpEndPoint) {
                    if (Optional.ofNullable(getProjectInfo().getWebServiceMap().get(key).get(generatePropertyFileMap().get(String.valueOf(DependencyEnum.unSecureURL)))).isPresent()) {
                        getProjectInfo().getWebServiceMap().get(key).get(generatePropertyFileMap().get(String.valueOf(DependencyEnum.unSecureURL))).add(finalEndPoint);
                    } else {
                        Set<String> webService = new HashSet<>();
                        webService.add(finalEndPoint);
                        getProjectInfo().getWebServiceMap().get(key).put(generatePropertyFileMap().get(String.valueOf(DependencyEnum.unSecureURL)), webService);
                    }
                } else {
                    if (Optional.ofNullable(getProjectInfo().getWebServiceMap().get(key).get((generatePropertyFileMap().get(String.valueOf(DependencyEnum.secureURL))))).isPresent()) {
                        getProjectInfo().getWebServiceMap().get(key).get(generatePropertyFileMap().get(String.valueOf(DependencyEnum.secureURL))).add(finalEndPoint);
                    } else {
                        Set<String> webService = new HashSet<>();
                        webService.add(finalEndPoint);
                        getProjectInfo().getWebServiceMap().get(key).put(generatePropertyFileMap().get(String.valueOf(DependencyEnum.secureURL)), webService);
                    }
                }
            } else {
                Map<String, Set<String>> endPointURLMap = new ConcurrentHashMap<>();
                Set<String> webService = new HashSet<>();
                webService.add(finalEndPoint);
                if (isHttpEndPoint) {
                    endPointURLMap.put(generatePropertyFileMap().get(String.valueOf(DependencyEnum.unSecureURL)), webService);
                } else {
                    endPointURLMap.put(generatePropertyFileMap().get(String.valueOf(DependencyEnum.secureURL)), webService);
                }
                getProjectInfo().getWebServiceMap().put(file.getPath(), endPointURLMap);
            }
        }
    }

    private boolean isContainEndPoint(String line) {
        if (Optional.ofNullable(line.contains(generatePropertyFileMap().get(String.valueOf(DependencyEnum.secureURL)))).get().booleanValue() ||
                Optional.ofNullable(line.contains(generatePropertyFileMap().get(String.valueOf(DependencyEnum.unSecureURL)))).get().booleanValue()) {
            return true;
        }
        return false;
    }

    private int getLastIndex(String line) {
        if (Optional.ofNullable(line.contains(generatePropertyFileMap().get(String.valueOf(DependencyEnum.secureURL)))).get().booleanValue()) {
            return line.lastIndexOf(generatePropertyFileMap().get(String.valueOf(DependencyEnum.secureURL)));
        } else if (Optional.ofNullable(line.contains(generatePropertyFileMap().get(String.valueOf(DependencyEnum.unSecureURL)))).get().booleanValue()) {
            return line.lastIndexOf(generatePropertyFileMap().get(String.valueOf(DependencyEnum.unSecureURL)));
        }
        return -1;
    }

    private String returnURLFromXMLFile(String line, File file) {
        if (!file.getName().contains(".xml")) {
            return line;
        }
        int begin = getLastIndex(line);
        String alter = line;
        if (begin > 0) {
            alter = line.substring(begin, line.length() - 1);

            int end = alter.indexOf('"');
            if (end > 0) {
                String finalMsg = alter.substring(0, end);

                return finalMsg;
            } else if (alter.contains("</Arg>")) {
                return alter.substring(0, alter.indexOf("</Arg>"));
            }

        }
        return line;
    }

    private File file;

    public void readLineByLine(String line) {
        findWebService(line, file);
        DetailedProjectInfo info = getProjectInfo().getDetailedProjectInfo().get(getProjectInfo().getDetailedProjectInfo().size() - 1);
        info.setNoOfLine(info.getNoOfLine() + 1);
        brackeFound = false;
        methodFlag = false;
        if (file.getName().contains(".java") && (line.contains("public") || line.contains("private") || line.contains("protected")) && line.contains(")") && line.contains("{")) {
            Arrays.stream(line.trim().split(" ")).forEach(this::fetchMethodName);
        }
    }

    private boolean methodFlag = false;

    public void fetchMethodName(String methodName) {
        if (brackeFound) {
            return;
        }
        DetailedProjectInfo info = getProjectInfo().getDetailedProjectInfo().get(getProjectInfo().getDetailedProjectInfo().size() - 1);
        if (methodName.contains("()")) {
            info.getListOfMethodName().add(removeBracketFromMethod(methodName));
            info.setNoOfMethod(info.getNoOfMethod() + 1);
            methodFlag = true;
        } else if (methodName.contains("(")) {
            methodFlag = true;
            info.getListOfMethodName().add(removeBracketFromMethod(methodName));
            info.setNoOfMethod(info.getNoOfMethod() + 1);
        } else if (methodFlag) {
            if (!info.getListOfMethodName().isEmpty()) {
                String methodSig = info.getListOfMethodName().get(info.getListOfMethodName().size() - 1);
                info.getListOfMethodName().remove(info.getListOfMethodName().size() - 1);
                info.getListOfMethodName().add(methodSig + " " + removeBracketFromMethod(methodName));
            }
            if (methodName.contains(")")) {
                methodFlag = false;
            }
        }
    }

    boolean brackeFound = false;

    public String removeBracketFromMethod(String method) {
        if (method.equalsIgnoreCase("{")) {
            brackeFound = true;
            return "";
        } else if (method.contains("{")) {
            brackeFound = true;
            String[] msg = method.split("\\{");
            if (msg.length > 0)
                return msg[0];
        }
        return method;
    }

    public void populateCacheInfo(XSSFWorkbook workbook) {
        if (getProjectInfo().getCacheConfigMap().isEmpty()) return;
        XSSFSheet sheet = workbook.createSheet("Cache Configuration");
        AtomicInteger rowCounter = new AtomicInteger(0);
        List<String> heading = new ArrayList<>(List.of(DependencyFinderConstant.FILE_NAME, "Cache Names",
                DependencyFinderConstant.COUNT));
        createExcelHeading(workbook, sheet, rowCounter.getAndIncrement(), heading);
        getProjectInfo().getCacheConfigMap().forEach((path, caches) -> {
            XSSFRow row = sheet.createRow(rowCounter.getAndIncrement());
            AtomicInteger cellCounter = new AtomicInteger();
            setCellvalueInExcel(path, row.createCell(cellCounter.getAndIncrement()), false, workbook);
            setCellvalueInExcel(caches.size(), row.createCell(cellCounter.getAndIncrement()), false, workbook);
        });

        int total = getProjectInfo().getCacheConfigMap()
                .values().stream().mapToInt(Set::size).sum();
        XSSFRow summary = sheet.createRow(rowCounter.getAndIncrement());
        setCellvalueInExcel("Total", summary.createCell(0), true, workbook);
        setCellvalueInExcel("", summary.createCell(1), false, workbook);
        setCellvalueInExcel(total, summary.createCell(2), false, workbook);

    }

    public void checkCacheDependencyInProject(File file) {
        if (file == null || !file.isFile()) return;

        String lowerName = file.getName().toLowerCase();
        boolean candidate = lowerName.endsWith(".xml") || lowerName.endsWith(".java")
                || lowerName.endsWith(".gradle") || lowerName.endsWith(".pom")
                || lowerName.endsWith(".properties") || lowerName.endsWith(".yaml")
                || lowerName.endsWith(".yml");
        if (!candidate) return;

        try (BufferedReader br = Files.newBufferedReader(file.toPath())) {
            boolean cacheCandidate = false;
            String content = br.lines()
                    .peek(line -> {
                        String l = line.toLowerCase();
                        if (l.contains("infinispan") || l.contains("jboss.cache") ||
                                l.contains("javax.cache") || l.contains("jcache")) {
                            // flag detection, real extraction handled below
                        }
                    })
                    .collect(Collectors.joining("\n"));
            // Instead of modifying global map directly, get per-file results
            Map<String, Set<String>> detected = detectCacheConfig(file, content);
            detected.forEach((path, caches) ->
                    getProjectInfo().getCacheConfigMap()
                            .computeIfAbsent(path, x -> new HashSet<>()).addAll(caches));
        } catch (IOException ex) {
            logger.debug("Cache dependency detection failed for: {}", file.getPath());
        }
    }


    private Map<String, Set<String>> detectCacheConfig(File file, String content) {
        if (file == null || content == null || content.isEmpty())
            return Collections.emptyMap();

        Set<String> found = new HashSet<>();
        String lowerContent = content.toLowerCase();
        String[] lines = content.split("\\R");

        for (String line : lines) {
            String l = line.toLowerCase();

            // XML cache configuration
            if (l.contains("<local-cache") || l.contains("<replicated-cache") ||
                    l.contains("<distributed-cache") || l.contains("<invalidation-cache") ||
                    l.contains("<cache-container") || l.contains("<infinispan")) {

                Matcher m = Pattern.compile("name\\s*=\\s*['\"]([^'\"]+)['\"]").matcher(line);
                if (m.find()) found.add(m.group(1));
                else if (l.contains("<local-cache")) found.add("local-cache");
                else if (l.contains("<replicated-cache")) found.add("replicated-cache");
                else if (l.contains("<distributed-cache")) found.add("distributed-cache");
                else if (l.contains("<invalidation-cache")) found.add("invalidation-cache");
            }

            // Java usage
            if (l.contains("org.infinispan") || l.contains("defaultcachemanager") ||
                    l.contains("new configurationbuilder")) {
                Matcher m1 = Pattern.compile("getcache\\([\"']([^\"']+)[\"']\\)").matcher(line);
                if (m1.find()) found.add(m1.group(1));

                Matcher m2 = Pattern.compile("defineconfiguration\\([\"']([^\"']+)[\"']").matcher(line);
                if (m2.find()) found.add(m2.group(1));
            }

            // JCache annotations
            if (l.contains("@cacheresult") || l.contains("@cacheput") ||
                    l.contains("@cacheremove") || l.contains("@cacheremoveall")) {
                found.add("jcache-annotation");
            }

            // YAML or properties
            Matcher yaml = Pattern.compile("(jboss|infinispan)\\.cache.*[:=]\\s*(\\w+)").matcher(line);
            if (yaml.find()) found.add(yaml.group(2));
        }

        if (found.isEmpty()) return Collections.emptyMap();
        return Map.of(file.getPath(), found);
    }
}
