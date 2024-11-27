---
lab:
    title: 'Lab 3: Power Automate'
    module: 'Module 2: Automate a business process using Power Automate'
---

# Practice Lab 3 - Power Automate

## Scenario

As we continue to build our solution, we will now create Power Automate cloud flows to process inspection data.

## High-level lab steps

We will create cloud flows to

- Reset all inspections to Pending.
- Clear comments from the canvas app.

## Things to consider before you begin

- Which triggers should you use?
- How to you use choice columns in expressions and actions?

## Starter solution

A starter solution file for this lab can be found in the C:\Labfiles\L03\Starter folder.

## Completed solution

Completed solution files for this lab can be found in the C:\Labfiles\L03\Completed folder.

## Exercise 1: Create a scheduled cloud flow

**Objective:** In this exercise, you will create a cloud flow that resets all inspections to Pending.

### Task 1.1: Create scheduled flow

1. Open the Permit Management solution.

   - Navigate to the Power Apps Maker portal `https://make.powerapps.com/`
   - Select your **Development** environment.
   - Select **Solutions**.
   - Open the **Permit Management** solution.

1. Create a scheduled cloud flow.

   - Select **+ New** and then select **Automation** > **Cloud flow** > **Scheduled**.

     ![new flow type - screenshot](../images/L03/new-scheduled-flow.png)

   - On the Welcome to Power Automate popup, select **Get started**.
   - Enter `Reset Inspections` for Flow name.
   - Change the **Repeat every** drop-down to **Month**.

     ![new flow name - screenshot](../images/L03/scheduled-flow-name.png)

   - Select  **Create**.

1. Add query step.

   - Select **+ New step**.
   - Search for `Dataverse` and select the **Microsoft Dataverse** connector.
   - Select the **List rows** action.

     ![Dataverse actions - screenshot](../images/L03/select-list-rows-action.png)

   - Rename the List rows step to `Inspections` by selecting the ellipses **...** on the flow step and selecting **Rename**.

     ![Rename flow step - screenshot](../images/L03/rename-flow-step.png)

   - Select **Inspections** for Table name.
   - Select **Show advanced options**.
   - In **Filter rows**, enter the OData query

     ```odata
     statuscode ne 330650001
     ```

     ![List rows step - screenshot](../images/L03/inspections-list-rows-step.png)

     > [!IMPORTANT]
     > 330650001 is the value of the Pending status reason on the Inspection table. You should verify that this is the correct value for your environment.

1. Add loop step.

   - Select **+ New step**.
   - Select the **Control** connector.
   - Select the **Apply to each** action.
   - Rename the step to `Loop`.
   - Click in **Select an output from previous steps**.
   - Using **Dynamic content**, select **value**.

     ![Apply to Each step Dynamic content - screenshot](../images/L03/configure-loop.png)

1. Add Update row step within the loop.

   - Select **+ Add an action**.
   - Search for `Dataverse` and select the **Microsoft Dataverse** connector.
   - Select the **Update a row** action.
   - Rename the step to `Update Inspection`.
   - Select **Inspections** for Table name.
   - Click in **Row ID**.
   - Using **Dynamic content**, select **Inspection**.

     ![Update row Dynamic content - screenshot](../images/L03/dynamic-content-inspection.png)

   - Select **Show advanced options**.
   - In **Status Reason**, select **Pending**.

     ![Reset inspections flow - screenshot](../images/L03/reset-inspections-flow.png)

   - Select **Save**.

### Task 1.2: Run flow

   ![Test flow - screenshot](../images/L03/test-flow.png)

1. Select **Test**.
1. Select **Manually**.
1. Select **Test**.
1. Select **Run flow**.
1. Select **Done**.
1. Select the **<-** icon.

### Task 1.3: Verify flow run

1. View inspection records

   - Navigate to the Power Apps Maker portal `https://make.powerapps.com/`
   - Select your **Development** environment.
   - Make sure you are in the Development environment.
   - Select **Apps**.
   - Select the **Permit Management** app, select the ellipses **...** and select **Play**.
   - Select **Inspections**.
   - Select **Edit columns**.
   - Select **+ Add columns**.
   - Select **Status Reason**.
   - Select **Close**.
   - Select **Apply**.
   - All inspections should be set to **Pending**.

   ![Inspection records - screenshot](../images/L03/pending-inspections.png)

## Exercise 2: Create an instant cloud flow

**Objective:** In this exercise, you will create a cloud flow that is run from a canvas app.

### Task 2.1: Create instant flow

1. Open the Permit Management solution.

   - Navigate to the Power Apps Maker portal `https://make.powerapps.com/`
   - Select your **Development** environment.
   - Select **Solutions**.
   - Open the **Permit Management** solution.

1. Create an instant cloud flow.

   - Select **+ New** and then select **Automation** > **Cloud flow** > **Instant**.
   - Enter `ClearInspectionComments` for Flow name.
   - Select the **When Power Apps calls a flow (V2)** trigger.

     ![new instant flow name - screenshot](../images/L03/instant-flow-name.png)

   - Select **Create**.

1. Add Input.

   - Select and expand the Power Apps trigger.
   - Select **+ Add an input**.
   - Select **Text**.
   - Click in *Input* and enter `InspectionID`.
   - Click in *Please enter your input* and enter `GUID`

     ![Power Apps Trigger input - screenshot](../images/L03/powerapps-trigger.png)

1. Add Update row step.

   - Select **+ New step**.
   - Search for `Dataverse` and select the **Microsoft Dataverse** connector.
   - Select the **Update a row** action.
   - Rename the step to `CancelComments`.
   - Select **Inspections** for Table name.
   - Click in **Row ID**.
   - Using **Dynamic content**, select **InspectionID**.

     ![Ask in Power Apps Dynamic content - screenshot](../images/L03/dynamic-content-use-input.png)

   - Select **Show advanced options**.
   - Click in **Comments**.
   - Select the **Expression** tab.
   - Enter `null`.

     ![Null expression - screenshot](../images/L03/null-expression.png)

   - Select **OK**.

1. Add Retrieve Permit step.

   - Select **+ New step**.
   - Search for `Dataverse` and select the **Microsoft Dataverse** connector.
   - Select the **Get a row by ID** action.
   - Rename the step to `GetPermit`.
   - Select **Permits** for Table name.
   - Click in **Row ID**.
   - Using **Dynamic content**, select **Permit (Value)**.

     ![Retrieve row Dynamic content - screenshot](../images/L03/dynamic-content-permit.png)

1. Add Respond to a Power App step.

   - Select **+ New step**.
   - Search for `Power Apps` and select the **Power Apps** connector.
   - Select the **Respond to a Power App or flow** action.
   - Select **+ Add an output**.
   - Select **Date**.
   - Click in *Enter title* and enter `startdate`.
   - Click in *Enter a value to respond*.
   - Using **Dynamic content**, search for `start` and select **Start Date**.

     ![Permit Start Date Dynamic content - screenshot](../images/L03/dynamic-content-start-date.png)

   - Select **Save**.
   - Select the **<-** icon.

### Task 2.2: Call instant flow from the canvas app

1. Open the Permit Management solution.

   - Navigate to the Power Apps Maker portal `https://make.powerapps.com/`
   - Select your **Development** environment.
   - Select **Solutions**.
   - Open the **Permit Management** solution.

1. Edit the **Inspector** canvas app.

   - Select **Apps** in the Permit Management solution.
   - Select the ellipses **...** next to **Inspector** app and select **Edit in new tab**.

1. Add the instant cloud flow to the app.

   - Select the ellipses **...** and then **Power Automate** tab.

     ![Add cloud flow to app - screenshot](../images/L03/add-flow-to-app.png)

   - Select **+ Add flow**.
   - Select **ClearInspectionComments**.

1. Add cancel comments button to the details screen.

   - Select the **Tree view** tab.
   - Select the **Details Screen** in the Tree view. Make sure that you have selected the screen and not selected the form.
   - Select the **+ Insert** tab and select **Button**.
   - Drag the button next to the failed button.
   - Select the **Tree view** tab.
   - Rename the button to `Clear Button`.
   - Change the **Text** value of the button to `"Clear Comments"`.
   - Resize the button to fit the added text.

     ![Form layout - screenshot](../images/L03/clear-button.png)

1. Run the cloud flow.

   - Select the **Clear Button** in the Tree view.
   - In the property selector, select **OnSelect**.
   - Change the **OnSelect** property to the formula below.

     ```powerappsfl
     Set(ScheduledDate, ClearInspectionComments.Run('Inspection List'.Selected.Inspection).startdate);Refresh(Inspections);
     ```

1. Add Text label.

   - Select the **+ Insert** tab.
   - Select **Text label**.
   - Drag the label to the top right of the screen.
   - Select the **Tree view** tab.
   - Rename **Label1** to `PermitStartLabel` by double clicking on the control in the Tree view tab.
   - Change the **Color** property of the **PermitStartLabel** label to `Color.White`.
   - Change the **Text** property of the **PermitStartLabel** to the formula below.

     ```powerappsfl
     ScheduledDate
     ```

1. Save the app.

### Task 2.3: Test the app

1. Start the application.

   - Select the **Main Screen** and select **Preview the app**.
   - The application should load and show at least one inspection.
   - Select the **Framing Inspection** record.
   - Select the **Clear Comments** button.
   - The inspection should be updated and the comments will be cleared.
   - **Close** preview mode.

1. Save and publish the app.

   - Select the **Publish** icon.
   - Select **Publish this version**.
   - Select the **<- Back** icon.
   - Select **Leave**.

## Exercise 3: Business process flow (Optional)

**Objective:** In this optional exercise, you will create a business process flow for the Build Site table with the following stages and steps:

- Stage 1: New Site with data steps for Street Address, City, State, Postal Code, Country
- Stage 2: Initial Permit on Permit table with data steps Build Site, Name, Contact, Start Date, Permit Type
- Stage 3: Initial Inspection on Inspection Table with data steps Permit, Name, Inspection Type, Scheduled Date

> [!NOTE]
> All data steps should be required

### Task 3.1: Create business process flow for Build Site

1. Open the Permit Management solution.

   - Navigate to the Power Apps Maker portal `https://make.powerapps.com/`
   - Select your **Development** environment.
   - Select **Solutions**.
   - Open the **Permit Management** solution.

1. Create a business process flow.

   - Select **+ New** and then select **Automation** > **Process** > **Business process flow**.
   - Enter `New Build Site` for Display name.
   - Enter `newbuildsite` for Name.
   - Select **Build Site** for Table.

     ![new business process flow name - screenshot](../images/L03/business-process-flow-name.png)

   - Select **Create**.

1. Add stages.

   - Select the first stage.
   - Enter `New Site` for Display Name.
   - Select **Apply**.
   - Select the **Components** tab.
   - Drag the **Stage** component and drop it onto the **+** icon to the right of the first stage.
   - Select the second stage.
   - Enter `Initial Permit` for Display Name.
   - Select **Permit** for Entity.
   - Select **Apply**.
   - Select the **+ Add** button in the command bar.
   - Select **Add Stage**.
   - Select the **+** icon to the right of the second stage.
   - Select the third stage.
   - Enter `Initial Inspection` for Display Name.
   - Select **Inspection** for Entity.
   - Select **Apply**.

     ![business process flow stages - screenshot](../images/L03/business-process-flow-stages.png)

1. Configure Build Site stage.

   - Select the first stage.
   - Expand **Details**.
   - Select the **+ Add** button in the command bar.
   - Select **Add Data Step**.
   - Select the **+** icon under the existing data step.
   - Repeat adding steps until there are five data steps on the stage.

     ![business process flow steps - screenshot](../images/L03/business-process-flow-data-steps.png)

   - Select **Data Step #1**.
   - Select **Street Address** for Data Field.
   - Enter `Street` for Step Name.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #2**.
   - Select **City** for Data Field.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #3**.
   - Select **State/Province** for Data Field.
   - Enter `State` for Step Name.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #4**.
   - Select **ZIP/Postal Code** for Data Field.
   - Enter `Postal Code` for Step Name.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #5**.
   - Select **Country/Region** for Data Field.
   - Enter `Country` for Step Name.
   - Check **Required**.
   - Select **Apply**.

     ![business process flow build site stage - screenshot](../images/L03/business-process-flow-build-site-stage.png)

1. Configure Initial Permit stage.

   - Select the second stage.
   - Expand **Details**.
   - Select the **+ Add** button in the command bar.
   - Select **Add Data Step**.
   - Select the **+** icon under the existing data step.
   - Repeat adding steps until there are five data steps on the stage.

   - Select **Data Step #1**.
   - Select **Build Site** for Data Field.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #2**.
   - Select **Name** for Data Field.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #3**.
   - Select **Contact** for Data Field.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #4**.
   - Select **Start Date** for Data Field.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #5**.
   - Select **Permit Type** for Data Field.
   - Check **Required**.
   - Select **Apply**.

     ![business process flow permit stage - screenshot](../images/L03/business-process-flow-permit-stage.png)

1. Configure Initial Inspection stage.

   - Select the third stage.
   - Expand **Details**.
   - Select the **Components** tab.
   - Drag the **Data Step** component and drop it onto the **+** icon under the existing data step.
   - Select the **Components** tab.
   - Drag the **Data Step** component and drop it onto the **+** icon under the existing data step.
   - Select the **Components** tab.
   - Drag the **Data Step** component and drop it onto the **+** icon under the existing data step.
   - There should be four data steps on the stage.

   - Select **Data Step #1**.
   - Select **Permit** for Data Field.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #2**.
   - Select **Name** for Data Field.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #3**.
   - Select **Inspection Type** for Data Field.
   - Check **Required**.
   - Select **Apply**.
   - Select **Data Step #4**.
   - Select **Scheduled Date** for Data Field.
   - Check **Required**.
   - Select **Apply**.

     ![business process flow permit stage - screenshot](../images/L03/business-process-flow-inspection-stage.png)

1. Save the business process flow.

     ![business process flow action bar - screenshot](../images/L03/business-process-flow-actions.png)

   - Select **Save**.
   - Select **Validate**. Validation should be successful.
   - Select **Activate**.
   - Select **Activate**.
   - Close the business process flow editor.
   - Select **Done**.

1. Add business process flow table to the solution.

   > [!IMPORTANT]
   > You need to add the Business process flow table to the solution to prevent dependency errors when importing the solution.

   - Select **Add existing** and select **Table**.
   - Search for `build` and select the **New Build Site** table.
   - Select **Next**.
   - Check **Include all objects**.
   - Select **Add**.

### Task 3.2: Test the business process flow

1. Create a Build Site.

   - Navigate to the Power Apps Maker portal `https://make.powerapps.com/`
   - Select your **Development** environment.
   - Select **Apps** and play the **Permit Management** app.
   - Select **Build Sites**.
   - Select **+ New**.

     ![business process flow for new record - screenshot](../images/L03/business-process-flow-new-record.png)

   - Select the **New Site** stage in the business process flow.
   - Enter `Street` for Street.
   - Enter `City` for City.
   - Enter `State` for State.
   - Enter `ZIP` for Postal Code.
   - Select **Save**.
   - Select the **New Site** stage in the business process flow.
   - Select **Next Stage**. You should see the error *Country: Required fields must be filled in*.

     > [!NOTE]
     > If the Next Stage button is not shown, refresh your browser.

     ![business process flow required step - screenshot](../images/L03/business-process-flow-required-step.png)

   - Enter `Country` for Country.

1. Transition to Permit stage.

   - Select **Next Stage**.
   - Select **+ Create**.
   - Select the **Initial Permit** stage in the business process flow.
   - Enter `BPF Permit` for Name.
   - Select **John Doe** for Contact.
   - Select today's date for Start Date.
   - Select **New Construction** for Permit Type.
   - Select **Save**.

1. Transition to Inspection stage.

   - Select the **Initial Permit** stage in the business process flow.
   - Select **Next Stage**.
   - Select **+ Create**.
   - Select the **Initial Inspection** stage in the business process flow.
   - Enter `BPF Inspection` for Name.
   - Select **Initial Inspection** for Inspection Type.
   - Select tomorrow's date for Scheduled Date.
   - Select **Save**.

## Exercise 4: Export and import solution

**Objective:** In this exercise, you will export the solution you created in the development environment and import it to the production environment.

### Task 4.1: Solution checker

1. Select the Permit Management solution.

   - Navigate to the Power Apps Maker portal `https://make.powerapps.com/`
   - Select your **Development** environment.
   - Select **Solutions**.
   - Select the **Permit Management** solution.

     ![Select solution - screenshot](../images/S03/select-solution.png)

1. Run solution checker.

   - Select **Solution checker** and then select **Run**.

     ![Run solution checker - screenshot](../images/S03/solution-checker-run.png)

   - Wait for the run to complete as it may take several minutes.

   - Open the **Permit Management** solution.

   - Select the **Overview** tab in the solution.

     ![Accessibility label - screenshot](../images/S03/solution-overview-tab.png)

   - Under **Solution status overview** then select **Run check** and wait for the run to complete.

   - There should be zero issues.

     ![Solution checker with zero issues result - screenshot](../images/S03/solution-checker-sero-issues.png)

### Task 4.2: Export solution

1. Export managed solution.

   - Select **Export**.

     ![Export solution - screenshot](../images/S03/solution-overview-export.png)

   - Select **Publish** and wait for the publishing to complete.

     ![Publish solution - screenshot](../images/S03/export-solution-publish.png)

   - Select **Next**.
   - Set the version number to `1.0.0.3`.
   - Select **Managed**.

     ![Export solution - screenshot](../images/S03/export-solution-managed.png)

   - Select **Export**.

     ![Export solution - screenshot](../images/S03/export-solution-download.png)  

   - Select **Download** to download the managed solution on your machine.

1. Export unmanaged solution.

   - Select **Export** again.
   - Select **Next**.
   - Edit the version number to match the Managed solution you just exported i.e., `1.0.0.3`.
   - Select **Unmanaged**.
  
    ![Export unmanaged solution - screenshot](../images/S03/export-solution-unmanaged.png)

   - Select **Export**.
   - Select **Download** to download the unmanaged solution on your machine.

### Task 4.3: Import solution

1. Import the Permit Management solution.

   - Navigate to the Power Apps Maker portal `https://make.powerapps.com/`
   - Select your **Production** environment.

1. Import solution.

   - Select **Solutions**.
   - Select **Import solution**.
   - Select **Browse**.
   - Select the **Managed** solution file you exported in the previous task and then select **Open**.

     ![Select solution file - screenshot](../images/S03/import-solution-file.png)

   - Select **Next**.
   - Select **Import**.
   - Wait for import to complete.
   - Navigate to both the model-driven and canvas apps you built and add a few records and test the apps.
