# BALDongleLib Documentation

## Requirements
Gradle version gradle 7.3.3 and higher.

## The BALLiberies will have multiple aar files, to use the lib download it and place these in your project dependency.
You can achieve this in following ways:-
1. add all the aar files in your project Libs or as a module by importing it into project in Android studio.
2. add the jitpack dependency like [jitpack](https://github.com/nkjha700011/repoD)

## Usage
The below sample of code is in Java. Syntax may vary in other languages like kotlin or typescript,as per platform , but the steps will be same as below.

# Step 1. create the BALBTDongleLib class instance like below
	BALBTDongleLib balBTDongleLib = new BALBTDongleLib(mInputStream,mOutputStream);
	# these parameter are
  
	@NonNull InputStream mInputStream, 
	@NonNull OutputStream mOutputStream 
	which app need to get form 'BluetoothSocket' instance of connected device.

# Step 1.1. once you have the instance of BALBTDongleLib then you need to initialize the dongle by calling the below function soon after the Step 1.
	boolean BTDongleComm = balBTDongleLib.initBTDongleComm(bt_dongle_name);
    # this parameter is : String bt_dongle_name
    # Note:
        1) invalid inputs for this function will be : null, " ", numeric input, special character
        2) valid input example: BAL_BTDongleR2_052  
# Step 1.2. To set the package directory so lib can access all the downloaded files in the application, call the below function call once Step 1.1 has returned true i.e. `BTDongleComm`
  	balBTDongleLib.setPackageDir(String packageDir);
    # Note:
        1) the packageDir should not be null or empty otherwise it will throw an exception "Package dir should not be null or empty".
        2) the packageDir should consist the valid filename otherwise it will throw an exception "Package dir should not be vaild path". 
## Note: Step 1. and Step 1.1 Step 1.2 call is mandatory. Call it together every time a connection is established

# Step 1.3. to stop the communication between dongle and application, call the below function
  	balBTDongleLib.stop();

# Step 2. List of all the methods along with implementaion details are given below. Call the methods as per the need

## Screen 1: bluetooth dongle pairing screen

### Method 1: to reset Dongle for other functionality, call the below function 
	balBTDongleLib.resetConfig();

## Screen 2: Read VIN 

### Method 2: to read a vin and return live data string, call the below function
	LiveData<String> liveReadVinData = balBTDongleLib.readVIN();


### Method 3: to validate if VIN is not Null and the length of vin is 17, call the below function
	boolean isvalid = balBTDongleLib.isValidVin(vin);
	# the parameter are: @NonNull String vin
   
### Method 3.1: to write a vin, call the below function and before calling this method call isValidVin function
	balBTDongleLib.writeVIN(vin);
	# this parameter is : @NonNull String vin
    # Note: The vin should not be empty string and should be a valid VIN i.e. its length should be 17.

## Screen 3: List of Ecu's

### Method 4: to get all the ECU records, call the funtion below and pass the downloaded Ecu records as string parameter to this function
	ArrayList<ECURecord> ecuRecordList= balBTDongleLib.getEcuRecords(ecuRecordsJson);
	# the parameter is : @NonNull String ecuRecordsJson
    # Note: The ecuRecordsJson should be a string in JSON format otherwise it will throw an exception. 

### Method 5: to get a particular ECU record, call the function below
	ECURecord ecuRecord = balBTDongleLib.getEcuRecord(pos);
	# the parameter is : int pos (position of the a particular record)
    # Note: The pos should not be a negative number and shoud be less than or equal to the size of ecuRecordsList that is formed in 'method 4'.

## Screen 4: Function selection

### Method 6: call this method on ecu clicked or on function selection load first call
    balBTDongleLib.getUDSParameter(ecuRecord);
    # the parameter is: ECURecord ecuRecord

### Method 7: to get the list of fault names from the parsed dtc xml file and return to update the data on UI,  call the function below
	ArrayList errorCodeList = balBTDongleLib.getListOfErrorCode(ecuRecord);
	# the parameter is: ECURecord ecuRecord
    # Note: 1) The ecuRecord parameter should not be null otherwise it will throw an exception and application will crash.
            2) parse the xml once and keep it ready to the filter data 

### Method 7.1: to send the parsed xml file data to vehicle to get the status of the list of faults, call the function below
    LiveData<ArrayList<ErrorCodeModel>> dtcErrorCode = balBTDongleLib.scanDtcErrorCode(ecuRecord);
    # the parameter is: ECURecord ecuRecord
    # Note: On call of this method it will parse the xml one time per session and on time out it will post the empty arraylist and immediately return the Live data

### Method 7.2: to get the list to DTC status types and show them on UI before clearing DTC, call the function below
    ArrayList<DtcStatusType> dtcStatusTypeList = balBTDongleLib.getDtcStatusTypeList();

### Method 7.3: to clear the active and inactive error codes, call the function below
	LiveData<String> clrErrCode= balBTDongleLib.clearErrorCode(ecuRecord,statusType);	
	# the parameter is: ECURecord ecuRecord, DtcStatusType statusType
    # Note: The statusType parameter will specify the status of the error it can be either (Active,InActive or Both).

### Method 8: to get DID groups, call the function below
	ArrayList<String> didGroups = balBTDongleLib.getDIDGroups(ecuRecord);
	# the parameter is: ECURecord ecuRecord

### Method 8.1: to get the list of DID read parameter names, call the function below
	ArrayList<ReadParameterModel> readParameterList = balBTDongleLib.getListOfReadParameter(ecuRecord,groupName);
	# the parameter is: ECURecord ecuRecord, String groupName
    # Note: 
        1) Both the parameters should not be null otherwise there will be a nullPointerException and application will crash.
        2) groupName should be a valid group name which should be present in didGroups List from 'Method 7'.
        3) ecuRecord should be a valid record which should be present in ecuRecordList from 'Method 4'.

### Method 9: to start the actuator routines, call the function below
	LiveData<String> srtActuRoutines = balBTDongleLib.startActuatorRoutines();

### Method 10: to start the analytics graph, call the function below
	LiveData<String> srtAnaGraph = balBTDongleLib.startAnalyticsGraph();

### Method 11: to update the bootloader, call the function below
	LiveData<String> updateBoot = balBTDongleLib.updateBootLoader();
### Method 11: to get the list of DID write parameter names, call the function below
	List<ReadParameterModel> writeParameterList = balBTDongleLib.getListOfWritableDidParameter(ecuRecord);
### Method 11.1: to write the DID write parameter value, call the function below
	balBTDongleLib.writeDidParameter(ecuRecord, readParameterModel, pos);	

### Method 11.1: to get the update regarding bootloader flashing, call the function below
    LiveData<FlashingUpdateModel> bootFlashingUpdate = balBTDongleLib.getBootFlashingUpdate(ecuRecord);
    # the parameter is: ECURecord ecuRecord 
    # Note: call the Method 5 to get ecuRecord

### Method 12: to get the update regarding flashing of any ECU, call the function below
    LiveData<FlashingUpdateModel> flashingUpdate = balBTDongleLib.getFlashingUpdate(ecuRecord);
    # the parameter is: ECURecord ecuRecord 
    # Note: call the Method 5 to get ecuRecord 

### Method 13: to get the list of wriable DID parameters, call the function below
    List<ReadParameterModel> listOfWritableDidParameter = balBTDongleLib.getListOfWritableDidParameter(ecuRecord);
    # the parameter is: ECURecord ecuRecord 
    # Note: call the Method 5 to get ecuRecord

### Method 13.1: to write the particular writable parameter, call the function below
    balBTDongleLib.writeDidParameter(ecuRecord,readParameterModel,pos)
    # the parameter is: ECURecord ecuRecord, ReadParameterModel readParameterModel, int pos
    # Note: call the Method 5 to get ecuRecord

### Method 14: to update the data returned by a synchronous call at later point on UI, so when you are updating call the function below so it will trigger the update for each UI call
    LiveData<String> uiDataUpdated = balBTDongleLib.updateUIDataUpdated();
    Note: register this call on every screen

# BALBTDongleApiLinkage Interface Implementaion is shown below

```
public interface BALBTDongleApiLinkage {
    public boolean initBTDongleComm(String bt_dongle_name);
    public void setPackageDir(Context context) throws Exception;
    public LiveData<String> readVIN();
    public boolean isValidVin(@NonNull String vin);
    public void writeVIN(String vin);
    public LiveData<ArrayList<ErrorCodeModel>> scanDtcErrorCode(ECURecord ecuRecord);
    public ArrayList<ECURecord> getEcuRecords(@NonNull String ecuRecordsJson);
    public ECURecord getEcuRecord( int pos);
    public ArrayList<DtcStatusType> getDtcStatusTypeList();
    public ArrayList getListOfErrorCode(ECURecord ecuRecord);
   
    public ArrayList<DtcStatusType> getDtcStatusTypeList();
    public LiveData<String> clearErrorCode(ECURecord ecuRecord);
   
    public void getUDSParameter(ECURecord ecuRecord);
    public ArrayList<String> getDIDGroups(ECURecord ecuRecord);
    public LiveData<String> getListOfReadParameter(ECURecord ecuRecord, String groupName);
    public LiveData<FlashingUpdateModel> getFlashingUpdate(ECURecord ecuRecord);
    public LiveData<FlashingUpdateModel> getBootFlashingUpdate(ECURecord ecuRecord);
    public LiveData<String> updateUIDataUpdated();
    public List<ReadParameterModel> getListOfWritableDidParameter(ECURecord ecuRecord);
    public void writeDidParameter(ECURecord ecuRecord, ReadParameterModel readParameterModel, int pos);
    public LiveData<String> startAnalyticsGraph();
    public LiveData<String> startActuatorRoutines();

    public LiveData<String> updateBootLoader();
    
    public void resetConfig();
    public void stop();

}

```
# BALBTDongleApiImpl class implementing the above interface is shown below

```
public class BALBTDongleApiImpl implements BALBTDongleApiLinkage {

    BALBTDongleLib balBTDongleLib;

    public BALBTDongleApiImpl(InputStream mInputStream, OutputStream mOutputStream)  {
        try {
            balBTDongleLib=new BALBTDongleLib(mInputStream,mOutputStream);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    public boolean initBTDongleComm(String bt_dongle_name) {
        boolean BTDongleComm=balBTDongleLib.initBTDongleComm(bt_dongle_name);
        return BTDongleComm;
    }

    @Override
    public void setPackageDir(Context context) throws Exception {
        String packageDir=context.getDataDir().getAbsolutePath();
        this.balbtDongleLib.setPackageDir(packageDir);
    }

    @Override
    public LiveData<String> readVIN() {
        LiveData<String> liveReadVinData = balBTDongleLib.readVIN();
        return liveReadVinData;
    }

    @Override
    public boolean isValidVin(@NonNull String vin) {
        boolean isvalid = balBTDongleLib.isValidVin(vin);
        return isvalid;
    }

    @Override
    public void writeVIN(String vin) {
        balBTDongleLib.writeVIN(vin);
    }

    @Override
    public ArrayList<ECURecord> getEcuRecords(@NonNull String ecuRecordsJson) {
        ArrayList<ECURecord> ecuRecordList= balBTDongleLib.getEcuRecords(ecuRecordsJson);
        return ecuRecordList;
    }

    @Override
    public ECURecord getEcuRecord(int pos) {
        ECURecord ecuRecord = balBTDongleLib.getEcuRecord(pos);
        return ecuRecord;
    }

    @Override
    public void getUDSParameter(ECURecord ecuRecord) {
         balbtDeviseDongle.getUDSParameter(ecuRecord);
    }

    @Override
    public ArrayList getListOfErrorCode(ECURecord ecuRecord) {
        ArrayList errorCodeList = balBTDongleLib.getListOfErrorCode(ecuRecord);
        return errorCodeList;
    }

    @Override
    public LiveData<ArrayList<ErrorCodeModel>> scanDtcErrorCode(ECURecord ecuRecord){
        return balbtDeviseDongle.scanDtcErrorCode(ecuRecord);
    }

    @Override
    public ArrayList<DtcStatusType> getDtcStatusTypeList() {
        return balbtDeviseDongle.getDtcStatusTypeList();
    }

    @Override
    public LiveData<String> clearErrorCode(ECURecord ecuRecord) {
        LiveData<String> clrErrCode= balBTDongleLib.clearErrorCode(ecuRecord);
        return clrErrCode;
    }

    @Override
    public ArrayList<String> getDIDGroups(ECURecord ecuRecord) {
        ArrayList<String> didGroups = balBTDongleLib.getDIDGroups(ecuRecord);
        return didGroups;
    }

    @Override
    public LiveData<String> getListOfReadParameter(ECURecord ecuRecord, String groupName) {
        LiveData<String> readParameterList = balBTDongleLib.getListOfReadParameter(ecuRecord,groupName);
        return readParameterList;
    }

    @Override
    public LiveData<String> startAnalyticsGraph() {
        LiveData<String> srtAnaGraph = balBTDongleLib.startAnalyticsGraph();
        return srtAnaGraph;
    }

    @Override
    public LiveData<String> startActuatorRoutines() {
        LiveData<String> srtActuRoutines = balBTDongleLib.startActuatorRoutines();
        return srtActuRoutines;
    }

    @Override
    public LiveData<String> updateBootLoader() {
        LiveData<String> updateBoot = balBTDongleLib.updateBootLoader();
        return updateBoot;
    }

    @Override
    public LiveData<FlashingUpdateModel> getBootFlashingUpdate(ECURecord ecuRecord) {
        return balbtDeviseDongle.getFlashingUpdate(ecuRecord);
    }

    @Override
    public LiveData<FlashingUpdateModel> getFlashingUpdate(ECURecord ecuRecord) {
        return balbtDeviseDongle.getFlashingUpdate(ecuRecord);
    }

    @Override
    public List<ReadParameterModel> getListOfWritableDidParameter(ECURecord ecuRecord) {
         return balbtDeviseDongle.getListOfWritableDidParameter(ecuRecord);
    }
    
    @Override
    public void writeDidParameter(ECURecord ecuRecord, ReadParameterModel readParameterModel, int pos) {
        balbtDeviseDongle.writeDidParameter(ecuRecord,readParameterModel,pos);
    }

    @Override
    public void resetConfig() {
        balBTDongleLib.resetConfig();
    }
    @Override
    public LiveData<ArrayList<ErrorCodeModel>> scanDtcErrorCode(ECURecord ecuRecord){
        return balbtDeviseDongle.scanDtcErrorCode(ecuRecord);
    }
    @Override
    public ArrayList<DtcStatusType> getDtcStatusTypeList() {
        return balbtDeviseDongle.getDtcStatusTypeList();
    }
    @Override
    public void getUDSParameter(ECURecord ecuRecord) {
         balbtDeviseDongle.getUDSParameter(ecuRecord);
    }
    @Override
    public LiveData<FlashingUpdateModel> getFlashingUpdate(ECURecord ecuRecord) {
        return balbtDeviseDongle.getFlashingUpdate(ecuRecord);
    }
    @Override
    public LiveData<FlashingUpdateModel> getBootFlashingUpdate(ECURecord ecuRecord) {
        return balbtDeviseDongle.getFlashingUpdate(ecuRecord);
    }
    @Override
    public LiveData<String> updateUIDataUpdated() {
        return balbtDeviseDongle.updateUIDataUpdated();
    }
    @Override
    public List<ReadParameterModel> getListOfWritableDidParameter(ECURecord ecuRecord) {
         return balbtDeviseDongle.getListOfWritableDidParameter(ecuRecord);
    }
    @Override
    public void writeDidParameter(ECURecord ecuRecord, ReadParameterModel readParameterModel, int pos) {
        balbtDeviseDongle.writeDidParameter(ecuRecord,readParameterModel,pos);
    }
    @Override
    public void stop() {
        balBTDongleLib.stop();
    }
}

```
