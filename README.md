# DemoRepo
demorepo
//this is the code for Parameterizing test cases using @DataProvider through Excel sheet column  headers
@Test(dataProvider="dpCreateNewCust")
  public void createNewCustomer(List<Map<String, String>> sheetList){   


            try{
                //Step 2. Login
                UtilityMethods.SignIn();

                for(Map<String, String> map : sheetList){
                    //Step 3. New Customer
                    if(map.get("Testcase").equals("Yes"))
                    {   
                        //Process with excel data
                        ProcessNewCustomer(map);                        
                    }
                }
            }
            catch(InterruptedException e)
            {
                System.out.println ("Login Exception Raised: <br> The exception get caught" + e);
            }           

  }

//My data provider 
@DataProvider(name = "dpCreateNewCust")  
    public  Object[][] dpCreateNewCust(){
       XLSfilename = System.getProperty("user.dir")+"//src//watts//XLSFiles//testcust.xlsx";
      List<Map<String, String>> arrayObject = UtilityMethods.getXLSData(XLSfilename,Sheetname));
        return new Object[][] { {arrayObject } };
    }

//----GetXLSData Method in UtilityMethods Class :
 public static List<Map<String, String>> getXLSData(String filename, String sheetname)
    {
        List<String> titleList = new ArrayList<String>();
    List<Map<String, String>> sheetList = new ArrayList<Map<String, String>>();
        try {     
            FileInputStream file = new FileInputStream(filename);

            //Get the workbook instance for XLS file 
            XSSFWorkbook XLSbook = new XSSFWorkbook(file);

            //Get first sheet from the workbook
            //HSSFSheet sheet = workbook.getSheetAt(0);
            WorkSheet = XLSbook.getSheet(sheetname);            

            //Iterate through each rows from first sheet
            int i = 0;
            Iterator<Row> rowIterator = WorkSheet.iterator();

            while(rowIterator.hasNext()) {
                Row row = rowIterator.next();

                //For each row, iterate through each columns
                Iterator<Cell> cellIterator = row.cellIterator();
                int j = 0;
                Map<String, String> valueMap = new HashMap<>();
                while(cellIterator.hasNext()) {

                    Cell cell = cellIterator.next();
                    if(i==0){
                        titleList.add(cell.getStringCellValue());
                    } 
                    else
                    {
                        String cellval = "";
                        switch(cell.getCellType()) {

                            case Cell.CELL_TYPE_BOOLEAN:
                                cellval = cell.getBooleanCellValue()+"";
                                break;
                            case Cell.CELL_TYPE_NUMERIC:
                                cellval = String.valueOf(cell.getNumericCellValue())+"";
                                break;
                            case Cell.CELL_TYPE_STRING:
                                cellval = cell.getStringCellValue();
                                break;
                            default:
                                 break;
                        }
                        if(cellval!="")
                        {
                            valueMap.put(titleList.get(j), cellval);                                                         valueMap.put("ResultRow",String.valueOf(row.getRowNum()));
                            valueMap.put("ResultCol",String.valueOf(0));
                        }
                    }
                    j++;
                }

                if(i!=0 && !valueMap.isEmpty()){
                    //System.out.println(valueMap);
                    sheetList.add(valueMap);
                }
                i++;
            }
            //System.out.println(sheetList);        System.exit(0);
            file.close();
            XLSbook.close();

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return sheetList;

    }
