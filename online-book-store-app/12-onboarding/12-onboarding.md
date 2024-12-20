# Improve User Onboarding

## Introduction

In this lab, you learn to create and manage a shopping cart within an Oracle APEX application. The lab includes adding navigation bar entry, creating a shopping cart page, implementing various interactive components, and integrating backend processes to handle cart operations and checkout procedures. This exercise will demonstrate the comprehensive capabilities of APEX for building dynamic web applications with a strong emphasis on database interaction and user interface design.

Estimated Time: 15 minutes

### Objectives

In this lab, you will:

- Email Verification for Forgot Password/User SignUp
- Reset Password/Signup
- Login with Google

## Task 1: Creating Email Verification for Forgot Password - Page 100002

Create the forgot password Page

1. On page designer toolbar, navigate to **(+ v)** and click **Page**.

    ![close dialog](images/12-1-create-page.png " ")

2. Click **Blank Page**.

    ![close dialog](images/blank-page-email.png " ")

3. Enter/select the following:

    - Under Page Definition:

        - Page Number: **100002**

        - Name: **Email Verification for Forgot Password**

    - Under Navigation:

        - Use Breadcrumb: **Toggle Off**

        - Use Navigation: **Toggle Off**

       Click **Create Page**.

    ![close dialog](images/12-1-email-page-details.png " ")

4. In the property editor, update the following:

     - Under Security > Authentication: **Page is Public**

    ![close dialog](images/12-1-page-public.png " ")

5. Right-click **Body** and select **Create Region**.

    ![close dialog](images/12-1-create-region-email.png " ")

6. In the property editor, enter/select the following:

    - Under Identification > Name: **Reset Password**

    ![close dialog](images/12-1-reset-pass.png " ")

7. Right-click **Reset Password** region and select **Create Page Item**.

    ![close dialog](images/12-1-create-page-item-email.png " ")

8. In the property editor, enter/select the following:

    - Under Identification > Name: **EMAIL**

    ![close dialog](images/12-1-page-item-name.png " ")

9. Right-click **EMAIL** and select **Create Validation**.

    ![close dialog](images/12-1-create-validation-email.png " ")

10. In the property editor, enter/select the following:

    - Under Identification > Name: **Valid Email Address**

    - Under Validation:

        - Type: **Item matches Regular Expression**

        - Item: **EMAIL**

        - Regular Expression : **^[a-zA-Z0-9+_.-]+@[a-zA-Z0-9.-]+(?:\.[a-zA-Z0-9-]+)*$**

    - Under Error:

        - Error Message: **Invalid Email Address!**

        - Associated Item: **-Select-**

    ![close dialog](images/12-1-valid-email.png " ")

11. Right-click **Validations** and select **Create Validation**.

    ![close dialog](images/12-1-create-valid1.png " ")

12. In the property editor, enter/select the following:

     - Under Identification > Name: **Email Validation**

     - Under Validation:

        - Type: **Rows returned**

        - SQL Query: Copy and paste the below query:

        ```
        <copy>
        select u.email from obs_users u  where u.email = lower(:EMAIL);
        </copy>
         ```

     - Under Error > Error Message: **No Account Found!**

     - Under Server-side Condition > Type: **Inline Validation Errors NOT displayed**

    ![close dialog](images/12-1-email-validation.png " ")

13. Right-click **EMAIL** and select **Create Button Below**.

    ![close dialog](images/12-1-create-button-below.png " ")

14. In the property editor, enter/select the following:

    - Under Identification:

        - Button Name: **Send**

        - Label: **Send Reset Password Link**

    - Under Appearance > Template Options: Click **Use Template Defaults**

        - Type: **Primary**

        Click **OK**

    ![close dialog](images/12-1-send-btn.png " ")

15. Navigate the **Processing** tab, right-click **Processing** and select **Create Process**.

    ![close dialog](images/12-1-create-process-email.png " ")

16. In the property editor, enter/select the following:

    - Under Identification:

        - Name: **Send Verification Email**

        - Type: **Invoke API**

    - Under Settings:

        - Package: **APEX_MAIL**

        - Procedure or Function: **SEND**

    - Under Success Message > Success Message: **Reset Password Link sent to your email address**.

    - Under Server-side Condition > When Button Pressed: **Send**

    ![close dialog](images/12-1-send-verification-email-process.png " ")

17. Under **Send Verification Email** process, expand **Parameters** and update the following:

    - **p\_to**:

        - Under Value:

             - Type: **Item**

             - Item: **EMAIL**

    ![close dialog](images/12-1-p-to.png " ")

    - **p\_from**:

        - Under Value:

            - Type: **Static Value**

            - Static Value: **noreply.obs@oracle.com**

    ![close dialog](images/12-1-p-from.png " ")

    - **p\_body**:

        - Under Value:

            - Type: **Static Value**

            - Static Value: **To view the content of this message, please use an HTML-enabled mail client.**

    ![close dialog](images/12-1-p-body.png " ")

    - **p\_body\_html**:

        - Under Value:

            - Type: **Function Body**

            - PL/SQL Function Body: Copy and Paste the below code:

            ```
            <copy>
            declare
                v_url  varchar2(1000);
            begin
                v_url := apex_util.host_url || APEX_PAGE.GET_URL (
                        p_page   => 100003,
                        p_items  => 'P100003_EMAIL',
                        p_values =>  :EMAIL);

                return '<html><body>' || utl_tcp.crlf ||

                            '<p>Please open the link to Reset Password for your account' || utl_tcp.crlf ||
                            '<p><a href="'|| v_url ||'">
                                <b> Reset Password </b></a></p>'|| utl_tcp.crlf ||
                            '<p>Sincerely,<br />' || utl_tcp.crlf ||
                            'The Online book store Team<br />' || utl_tcp.crlf ||

                            '</body></html>';

            end;
            </copy>
            ```

    ![close dialog](images/12-1-p-body-html.png " ")

    - **p\_subj**:

        - Under Value > Type: **API Default**

        - Under Comments > Comments: **Reset Password**

    ![close dialog](images/12-1-p-subt.png " ")

18. Right-click **After Processing** and select **Create Branch**.

    ![close dialog](images/12-1-create-branch-email.png " ")

19. In the property editor, enter/select the following:

    - Under Identification > Name: **Go To Login Page**

    - Under Behavior:
        - Target: Click **No Link Defined**

            - Page: **9999**

            Click **OK**.

    ![close dialog](images/12-1-go-to-login.png " ")

20. Click **Save**.

    Update the Login Page

21. Navigate to Page - 9999.

    ![close dialog](images/12-1-login-9999.png " ")

22. Under **Online Bookstore** region, right-click **Next** and select **Create Button**.

    ![close dialog](images/12-1-create-btn-login.png " ")

23. In the Property editor, enter/select the following:

    - Under Identification:

        - Button Name: **Forgot_Password**

        - Label: **Forgot Password**

    - Under Appearance > Template options: Click **Use Template Defaults**
        - Size: **Small**

        - Type: **Danger**

        - Width: **Stretch**

        - Spacing Top: **Small**

          Click **OK**.

    - Under Behavior:

        - Action: **Redirect to page in this application**

        - Target: Click **No Link Defined**

            - Page: **100002**

            - Clear Cache: **100002**

            Click **OK**.

    ![close dialog](images/12-1-forgot-pass-btn.png " ")

    ![close dialog](images/12-1-forgot-pass-link.png " ")

24. Click **LOGIN** button and update the following:

    - Under Appearance > Template options: Click **Use Template Defaults**

        - Type: **Success**

          Click **OK**.

    ![close dialog](images/12-1-login-btn.png " ")

25. Click **Save**. 

## Task 2: Creating  Reset Password - Page 100003

1. On page designer toolbar, Navigate to **(+ v)** and click **Page**.
    ![close dialog](images/12-2-blank-page-email.png " ")

2. Click **Blank Page**.

    ![close dialog](images/blank-page-email.png " ")

3. Enter/select the following:

    - Under Page Definition:

        - Page Number: **100003**

        - Name: **Reset Password**

    - Under Navigation:

        - Use Breadcrumb: **Toggle Off**

        - Use Navigation: **Toggle Off**

       Click **Create Page**.

    ![close dialog](images/12-2-create-reset-pass.png " ")

4. In the Property editor, update the following:

    - Under Security > Authentication: **Page is Public**

    ![close dialog](images/12-2-rest-page-public.png " ")

5. In the left pane, right-click **Body** and click **Create Region**.

   ![close dialog](images/12-2-create-region-reset.png " ")

6. In the Property editor, enter/select the following:

    - Under Identification:

        - Name: **Update Current User**

        - Type: **Form**

    - Under Source > Table Name: **OBS_USERS**

    ![close dialog](images/12-2-update-current-user1.png " ")

7. Under **Update Current User** region, select all page items except **P100003\_USER\_ID**, **P100003\_EMAIL** and **P100003\_PASSWORD**, right-click and select **Delete**.

   ![close dialog](images/12-2-del-page-items.png " ")

8. Select **P100003\_EMAIL** and update the following:

    - Under Identification > Type: **Display Only**

    - Under Source > Primary Key: **Toggle On**

    - Under Security > Session State Protection: **Unrestricted**

    ![close dialog](images/12-2-email-details.png " ")

9. Select **P100003\_PASSWORD** and update the following:

    - Under Identification > Type: **Password**

    ![close dialog](images/12-2-pass-type.png " ")

10. Right-click **P100003\_PASSWORD** and click **Create Validation**.

    ![close dialog](images/12-2-pass-validation.png " ")

11. In the Property editor, enter/select the following:

    - Under Identification > Name: **Not Null Validation**

    - Under Validation:

        - Type: **Function Body (Returning Boolean)**

        - PL/SQL Function Body: Copy and paste the below code:

         ```
        <copy>
        if :P100003_Password is not NULL then
            return true;
        else
            return false;
        end if;
       </copy>
        ```

        - Under Error > Error Message: **Password field should have some value.**

    ![close dialog](images/12-2-not-null-valid.png " ")

12. Right-click **Update Current User** and select **Create Page Item**.

    ![close dialog](images/12-2-page-item-reset.png " ")

13. In the Property editor, enter/select the following:

    - Under Identification:

        - Name: **P100003\_CONFIRM\_PASSWORD**

        - Type: **Password**

    ![close dialog](images/12-2-confirm-pass-reset.png " ")

14. Right-click **P100003\_CONFIRM\_PASSWORD** and select **Create Validation**.

    ![close dialog](images/12-2-create-val-con.png " ")

15. In the Property editor, enter/select the following:

    - Under Identification > Name: **Not Null Validation1**

    - Under Validation:

        - Type: **Function Body (Returning Boolean)**

        - PL/SQL Function Body: Copy and paste the below code:

        ```
        <copy>
        if :P100003_CONFIRM_PASSWORD is not NULL then
            return true;
        else
            return false;
        end if;
        </copy>
       ```

    - Under Error > Error Message: **Password field should have some value.**

    ![close dialog](images/12-2-not-null-cf.png " ")

16. Again, right-click **P100003\_CONFIRM\_PASSWORD** and select **Create Validation**.
     ![close dialog](images/12-2-con-pass-create-vald.png " ")
17. In the Property editor, enter/select the following:

    - Under Identification > Name: **Compare Passwords**

    - Under Validation:

        - Type: **Function Body (Returning Boolean)**

        - PL/SQL Function Body: Copy and paste the below code:

            ```
            <copy>
            begin
             if :P100003_Password = :P100003_CONFIRM_PASSWORD then
                return true;
             else
                return false;
             end if;
            end;
            </copy>
           ```

    - Under Error > Error Message: **Confirm Password is not same as Password entered above.**

    ![close dialog](images/12-2-compare-pass-valid.png " ")

18. Right-click **Update Current User** region and select **Create Sub-Region**.

    ![close dialog](images/12-2-sub-region-reset.png " ")

19. In the Property editor, enter/select the following:

    - Under Identification > Name: **Buttons**

    - Under Layout > Slot: **Region Body**

    - Under Appearance:

        - Template: **Buttons Container**

        - Template option: **Use Template Options**

            - Style: **Remove UI Decoration**

            Click **OK**.

    ![close dialog](images/12-2-button-reset.png " ")

20. Right-click **Buttons** and select **Create Button**.

    ![close dialog](images/12-2-create-btn-reset.png " ")

21. In the Property editor, enter/select the following:

    - Under Identification > Button Name: **CANCEL**

    - Under Layout > Slot: **Close**

    - Under Behavior:

        - Action: **Redirect to page in this Application**

        - Target: Click **No Link Defined**

            - Page: **15010**

            - Clear Cache: **100003**

            Click **OK**.

    ![close dialog](images/12-2-cancel-reset.png " ")

22. Right-click **CANCEL** and select **Create Dynamic Action**.

    ![close dialog](images/12-2-create-dyn-act-reset.png " ")

23. In the Property editor, enter/select the following:

    - Under Identification > Name: **Cancel Dialog**

    - Under When > Event: **Click**

    ![close dialog](images/12-2-canel-dialog-reset.png " ")

24. Right-click **Buttons** and select **Create Button**.

    ![close dialog](images/12-2-create-button-save.png " ")

25. In the Property editor, enter/select the following:

    - Under Identification:

        - Button Name: **SAVE**

        - Label: **Apply Changes**

    - Under Layout > Slot: **Next**

    - Under Appearance > Hot: **Toggle On**

    - Under Behavior > Database Action: **SQL UPDATE action**

    ![close dialog](images/12-2-save-btn-reset.png " ")

26. Select  **Buttons** and in the Property editor, update the following:

    - Under Layout > Sequence: **70**

    ![close dialog](images/12-2-buttons-layout.png " ")

27. Navigate to **Processing** tab, right-click **Processing** and select **Create Process**.

    ![close dialog](images/12-2-create-process-reset.png " ")

28. In the Property editor, enter/select the following:

    - Under Identification:

        - Name: **Process from Update current user**

        - Type: **Form - Automatic Row Processing (DML)**

        - Form Region: **Update current user**

    - Success Message > Success Message: **Updated Profile details successfully!**

    ![close dialog](images/12-2-current-user-process.png " ")

29. Right-click **After Processing** and select **Create Branch**.

    ![create-branch](images/12-2-create-branch.png " ")

30. In the property editor, enter/select the following:

    - Under Identification > Name: **Go to My Profile**

    - Under Behavior > Target: Click **No Link Defined**

         - Page: **12**

         - Clear Cache: **1**

         Click **OK**

    ![branch-link](images/12-2-branch-details.png " ")

31. Click **Save**.

32. On page designer toolbar, Navigate to **(+ v)** and select **Page**.

    ![user-create-page](images/12-2-nav-create-page.png " ")

33. Click **Blank Page**.

    ![user-blank-page](images/12-2-create-blank-page-1.png " ")

34. Enter/select the following:

    - Under Page Definition:

        - Page Number: **12**

        - Name: **LOGIN_HOME**

    - Under Navigation:

        - Use Breadcrumb: **Toggle Off**

        - Use Navigation: **Toggle Off**

       Click **Create Page**.

    ![Login Home Page Create](images/12-2-page12-details.png " ")

35. Right-click **Before Header** under **Pre-rendering** and click **Create Branch**

    ![branch-link](images/12-2-create-branch1.png " ")

36. In the property editor, enter/select the following:

    - Under Identification > Name: **Go To Home Page**
    - Under Behavior > Target: Click **No Link Defined**

         - Page: **10**

         Click **OK**

    ![branch-link](images/12-2-branch-link1.png " ")

37. Click **Save**.

## Task 3: Creating Email Verification for User SignUp - Page 100001

Create the user signUp Page

1. On page designer toolbar, Navigate to **(+ v)** and click **Page**.

    ![form-page-user-signup](images/12-3-nav-create-page.png " ")

2. Choose **Form Page**.

    ![form-page-user-signup](images/12-3-create-form-page.png " ")

3. Enter/select the following:

    - Under Page Definition:

        - Page Number: **100001**

        - Name: **Email Verification for User SignUp**

    - Under Data Source:

        - Table/View Name: **OBS\_UNVERIFIED\_USERS**

    - Under Navigation:

        - Use Breadcrumb: **Toggle Off**

        - Use Navigation: **Toggle Off**

    Click **Next**.

    ![user-signup-page-details](images/12-3-page-prop.png " ")

    - Under Branch Pages:

        - Branch Here on Submit: **9999**

        - Cancel and Go To Page: **9999**

    Click **Create Page**.

    ![user-signup-page-details](images/12-3-page-prop1.png " ")

4. In the property editor, update the following:

     - Under Security > Authentication: **Page is Public**

    ![page-public](images/12-3-page-public.png " ")

5. Under **Email Verification for User SignUp** region, select **DELETE** and **SAVE** buttons, right-click and select **Delete**.

    ![delete-buttons](images/12-3-delete-buttons.png " ")

6. Right-click **P100001\_EMAIL** and select **Create Validation**.

    ![create-validation](images/12-3-email-create-vald.png " ")

7. In the property editor, enter/select the following:

    - Under Identification > Name: **If Email Already Present**

    - Under Validation:

        - Type: **No Rows returned**

        - SQL Query: Copy and paste the below query:

        ```
        <copy>
        select email from obs_users where email = lower(:P100001_EMAIL);
        </copy>
         ```

    - Under Error > Error Message: **Account already existed for this email ID.**

    - Under Server-side Condition > Type: **Inline Validation Errors NOT displayed**

    ![valid-email](images/12-3-email-vald-prop.png " ")

8. Under **Email Verification for User SignUp** region, select **CREATE** button and update the following:

    - Under Identification > Label: **Send SignUp Email**

    ![valid-email](images/12-3-create-button-label.png " ")

9. Navigate to **Processing** tab, select **Process form Email Verification for User SignUp** under **Processing** and update the following:

    - Under Success Message > Success Message: **Email Sent**

    ![success-message](images/12-3-nav-process.png " ")

10. Right-click **Processing** and select **Create Process**.

    ![create-process](images/12-3-create-processs.png " ")

11. In the property editor, enter/select the following:

    - Under Identification:

        - Name: **Send Verification Email**

        - Type: **Invoke API**

    - Under Settings:

        - Package: **APEX_MAIL**

        - Procedure or Function: **SEND**

    - Server-side Condition > When Button Pressed: **CREATE**

    ![send-verification-email-process](images/12-3-processs-prop.png " ")

12. Under **Send Verification Email** process, expand **Parameters** and update the following:

    - **p\_to**:

        - Under Value:

             - Type: **Item**

             - Item: **P100001\_EMAIL**

    ![p-to](images/12-3-p-to.png " ")

    - **p\_from**:

        - Under Value:

            - Type: **Static Value**

            - Static Value: **noreply.obs@oracle.com**

    ![p-from](images/12-3-p-from.png " ")

    - **p\_body**:

        - Under Value:

            - Type: **Static Value**

            - Static Value: **To view the content of this message, please use an HTML-enabled mail client.**

    ![p-body](images/12-3-p-body.png " ")

    - **p\_body\_html**:

        - Under Value:

            - Type: **Function Body**

            - PL/SQL Function Body: Copy and Paste the below code:

            ```
            <copy>
            declare
            l_url varchar2(1000);
            begin
            l_url := apex_util.host_url || APEX_PAGE.GET_URL (
            p_page  => 100000,
            p_items => 'P100000_EMAIL',
            p_values => :P100001_EMAIL);

            return '<html><body>' || utl_tcp.crlf ||
            '<p>Please open the link to create your account' || utl_tcp.crlf ||
            '<p><a href="'|| l_url ||'">
            <b> Setup your account </b></a></p>'|| utl_tcp.crlf ||
            '<p>Sincerely,<br />' || utl_tcp.crlf ||
            'The Online book store Team<br />' || utl_tcp.crlf ||
            '</body></html>';

            end;
            </copy>
            ```

    ![p-body-html](images/12-3-p-body-html-param.png " ")

    - **p\_subj**:

        - Under Value:

            - Type: **Static Value**

            - Static Value: **Set up your online bookstore account**.

    ![p-subt](images/12-3-p-subj.png " ")

13. Click **Save**.

    Update the Login Page

14. Navigate to Page - **9999**.

    ![login-9999](images/12-3-nav-page9999.png " ")

15. Under **Online Bookstore** region, right-click **Next** and select **Create Button**.

    ![create-btn-login](images/12-3-create-button-signup.png " ")

16. In the Property editor, enter/select the following:

    - Under Identification:

        - Button Name: **SignUp**

        - Label: **New here? SignUp**

    - Under Appearance > Template options: Click **Use Template Defaults**

        - Size: **Small**

        - Style: **Display as Link**

          Click **OK**.

    ![image](images/12-3-signup-button-prop1.png " ")

    - Under Behavior:

        - Action: **Redirect to page in this application**

        - Target: Click **No Link Defined**

            - Page: **100001**

            Click **OK**.

    ![image](images/12-3-signup-button-prop1.png " ")

17. Click **Save**.

## Task 4: Creating  SignUp - Page 100000

1. On page designer toolbar, Navigate to **(+ v)** and click **Page**.
    ![form-page-signup](images/12-4-nav-create-page.png " ")

2. Choose **Form Page**.

    ![form-page-signup](images/12-4-select-form-page.png " ")

3. Enter/select the following:

    - Under Page Definition:

        - Page Number: **100000**

        - Name: **SignUp**

    - Under Data Source:

        - Table/View Name: **OBS\_USERS**

    - Under Navigation::

        - Use Breadcrumb: **Toggle Off**

        - Use Navigation: **Toggle Off**

    Click **Next**.

    ![signup-page-details](images/12-4-form-page-details.png " ")

    - Under Branch Pages:

        - Branch Here on Submit: **10**

        - Cancel and Go To Page: **10**

    Click **Create Page**.

    ![signup-page-details](images/12-4-form-page-details1.png " ")

4. In the Property editor, update the following:

    - Under Security > Authentication: **Page is Public**

    ![page-public](images/12-4-page-public.png " ")

5. Under **SignUp** region, select page items **P100000\_MIME\_TYPE** and **P100000\_PICTURE\_URL**, right-click and select **Delete**.

    ![page-public](images/12-4-delete-page-items.png " ")

6. Select **P100000\_USERNAME**, **P100000\_PASSWORD**, **P100000\_FULL\_NAME** and update the following:

    - Under Identification > Type: **Text Field**

    ![page-public](images/12-4-page-items-text-field.png " ")

7. Select **P100000\_EMAIL** and under Identification update Type: **Display Only**

    ![page-public](images/12-4-email-prop.png " ")

8. Select **P100000\_PASSWORD** and in the property editor, update the following:

    - Under Identification > Type: **Password**
    - Under Appearance > Template: **Required - Floating**
    - Under Validation > Value Required: **Toggle On**

    ![page-public](images/12-4-password-prop.png " ")

9. Select **P100000\_PROFILE\_PIC** and in the Property editor, enter/select the following:

    - Under Identification > Type: **Image Upload**

    - Under Storage > MIME Type Column: **MIME_TYPE**

    ![page-public](images/12-4-profile-pic-prop.png " ")

10. Select **P100000\_IS\_ADMIN** and in the Property editor update the following:

    - Under Identification > Type: **Hidden**

    ![page-public](images/12-4-is-admin-prop.png " ")

11. Under **SignUp** region, select **DELETE** and **SAVE** buttons, right-click and select **Delete**.

    ![page-public](images/12-4-buttons-delete.png " ")

12. Select **CREATE** button and under Identification update Label: **SignUp**.

    ![page-public](images/12-4-create-label.png " ")

13. Navigate to **Processing** tab, right-click **Processing** and select **Create Process**.

    ![page-public](images/12-4-create-process.png " ")

14. In the property editor, enter/select the following:

    - Under Identification:

        - Name: **Remove From Unverified Users Table**

    - Under Sources

        - PL/SQL Code: Copy and paste the below code:

        ```
        <copy>
        delete from obs_unverified_users where email= lower(:P100000_EMAIL);
        </copy>
         ```

    ![page-public](images/12-4-process-prop.png " ")

15. Right-click **Processing** and select **Create Process**.

    ![page-public](images/12-4-create-process1.png " ")

16. In the property editor, enter/select the following:

    - Under Identification:

        - Name: **Set Username Cookie**

        - Type: **Invoke API**

    - Under Settings:

        - Package: **APEX\_AUTHENTICATION**

        - Procedure or Function: **SEND\_LOGIN\_USERNAME\_COOKIE**

    - Server-side Condition > When Button Pressed: **CREATE**

    ![page-public](images/12-4-process1-prop.png " ")

17. Under **Set Username Cookie** process, expand **Parameters** and update the following:

    - Select **p\_username**:

        - Under Value:

            - Type: **Expression**

            - PL/SQL Expression: **lower(:P100000_EMAIL)**

    ![page-public](images/12-4-p-username.png " ")

    - Select **p\_cookie\_name**, right-click and select **Delete**

    ![page-public](images/12-4-p-cookie-name.png " ")

18. Right-click **Processing** and select **Create Process**.

    ![page-public](images/12-4-create-process2.png " ")

19. In the property editor, enter/select the following:

    - Under Identification:

        - Name: **Login**

        - Type: **Invoke API**

    - Under Settings:

        - Package: **APEX_AUTHENTICATION**

        - Procedure or Function: **LOGIN**

    ![page-public](images/12-4-process2-prop.png " ")

20. Under **Login** process, expand **Parameters** and update the following:

    - Select **p\_username**:

        - Under Value:

            - Type: **Item**

            - Item: **P100000_EMAIL**

    ![page-public](images/12-4-username.png " ")

21. Right-click **Processing** and select **Create Process**.

    ![page-public](images/12-4-create-process3.png " ")

22. In the property editor, enter/select the following:

    - Under Identification:

        - Name: **Clear Page(s) Cache**

        - Type: **Clear Session State**

    - Under Server-side Condition:

        - When Button pressed: **CREATE**

    ![page-public](images/12-4-process3-prop.png " ")

23. Click **Save**.

## Task 5: Login with Google

 You create a Social Sign-in authentication scheme in this task to enable Google Authentication.

1. Log in to Google's developer [console](https://console.developers.google.com).

     *Note: If you are logging in to the Google developer console for the first time, you must check and click on AGREE AND CONTINUE*

2. Click **CREATE PROJECT**.

    ![Click My Apps](images/create-project1.png " ")

3. In the New Project Screen, For Project Name: Enter **OBS Application** and click **CREATE**.

    ![Click My Apps](images/new-project1.png " ")

4. Click the OAuth consent screen (from the left side menu), select **External**, and click **CREATE**

    ![Click My Apps](images/external.png " ")

5. In the OAuth consent screen, Enter the following:

   Under **App Information** Section:

      - For Application name: **Online Bookstore**

      - For User support email: Enter your **Email Address**

   Under **App Domain** Section:

      - For Application Homepage link: Enter your **Application Homepage link**

   Under **Authorized domains** Section:

      - Click **+ADD DOMAIN** and add your Authorized domain. For example, I added oracle.com

   Under **Developer contact information** Section:

     - For Email addresses: Enter your **Email Address**

    ![Click My Apps](images/oauth-consent-screen.png " ")

    ![Click My Apps](images/oauth-consent-screen1.png " ")

   Click **SAVE and CONTINUE**.

6. In Scopes, leave everything as default and click **SAVE and CONTINUE**.

    ![Click My Apps](images/scopes.png " ")

7. In Test users, leave everything as default and click **SAVE and CONTINUE**.

    ![Click My Apps](images/test-users.png " ")

8. Click **Credentials** (from left side menu). Now click **+Create Credentials** and select **OAuth client ID**.

    ![Click My Apps](images/create-creds.png " ")

9. Enter the following:

     - For Application type: Select **Web Application**

     - For Name: Enter **Online Bookstore Authentication**

   Under Authorized redirect URIs, click **+Add URl**

     - For URls 1: Enter https://apex.oracle.com/pls/apex/apex_authentication.callback

     Click **CREATE**.

    ![Click My Apps](images/create-creds1.png " ")

10. You will get the **Client ID** and **Client secret**. Save these IDs. We will use them later.

    ![Click My Apps](images/creds-created.png " ")

11. Login into your Oracle APEX workspace.

12. On the Workspace home page, click **App Builder**.

    ![Click My Apps](images/12-5-12-app-builder.png " ")

13. Click **Workspace Utilities**.

    ![Click My Apps](images/12-5-13-workspace-utilities.png " ")

14. Click **Web Credentials**.

    ![Click My Apps](images/12-5-14-web-cred.png " ")

15. Click **Create**.

    ![Click My Apps](images/12-5-15-create.png " ")

16. In the **Web Credentials** enter the following and click **Create**.

    - Under **Attributes**:

        - Name: Enter **Google Authentication**
        - Static Id: **Google_Authentication**
        - Authentication Type: Select **OAuth2 Client Credentials**.
        - Client ID or Username: Enter the **Client ID** you copied in **Step 10**.
        - Client Secret or Password: Enter the **Client secret** you copied in **Step 10**.
        - Verify Client Secret or Password: Enter the **Client secret** you copied in **Step 10**.

    Click **Create**

    ![Define Web Credentials](images/create-web-cred1.png " ")

17. Navigate to **App Builder** and select **Online Bookstore Application**.

    ![Navigate to Online bookstore application](images/navigate-to-osa.png " ")

18. Click **Shared Components**.

    ![Select Shared Components](images/select-shared-components.png " ")

19. Under **Security**, click **Authentication Schemes**.

    ![Select Authentication Schemes](images/select-authentication.png " ")

20. In the **Authentication Schemes** page, click **Create**.

    ![Click Create](images/click-create2.png " ")

21. Under **Create Authentication Scheme** Page, leave the settings to default and click **Next**.

    ![Create Authentication Scheme](images/create-auth1.png " ")

22. In the **Authentication Scheme**, Enter the following:
    Under **Name**:
    - Name: **GOOGLE**.
    - Scheme Type: **Social Sign-In**.

    Under **Settings**:
    - Credential Store: **Google Authentication**
    - Authentication Provider: Select **Google**
    - Scope: **profile,email**
    - Username: **email**
    - Additional User Attributes: **profile,picture,email,username**

    Click **Create Authentication Scheme**

    ![Define Authentication](images/create-auth2.png " ")

23. Notice that a new **Authentication Scheme** you created is displayed. Click **GOOGLE**.

    ![Authentication scheme displayed](images/create-auth3.png " ")

24. In the Property editor, enter/select the following:

    - Under Login Processing:

        - Post-Authentication Procedure Name: **OBS\_AUTH.google\_post\_authenticate**

        - Switch in Session: **Enabled**

    Click on **Apply Changes**

    ![Authentication scheme displayed](images/12-5-24-google1.png " ")

25. Navigate to the **SQL Workshop** > **Object Browser** > **Package** > **OBS\_AUTH**

     ![Authentication scheme displayed](images/12-5-25-nav-obj-browser.png " ")

26. Add the following code to the spec and body of the package above the **'end "OBS_AUTH";'** and Click **Save and Continue**.

    - Specification: **procedure google\_post\_authenticate;**

    - Body: paste the below code.

        ```
        <copy>
        procedure  google_post_authenticate is
        l_email varchar2(1000);
        L_VAR VARCHAR2(100);
        l_user_id number;
        l_username varchar2(1000);
        Begin
            l_email:= apex_json.get_varchar2('email');

            begin
            select user_id, username into l_user_id,l_username from obs_users where email=l_email;

            update obs_users set full_name =  apex_json.get_varchar2('name'), picture_url = apex_json.get_varchar2('picture') where user_id = l_user_id;
            apex_custom_auth.set_user (
            p_user => l_username
        );
            exception when no_data_found then

            insert into obs_users(email,username,full_name,picture_url) values (
                        apex_json.get_varchar2('email'),
                        apex_json.get_varchar2('email'),
                        apex_json.get_varchar2('name'),
                        apex_json.get_varchar2('picture')
                    );
        end;
        end google_post_authenticate;
        </copy>
        ```

    ![Authentication scheme displayed](images/12-5-26-spec.png " ")
    ![Authentication scheme displayed](images/12-5-26-body.png " ")

27. Navigate to Application home page and select **9999 - Login Page**.

    ![Authentication scheme displayed](images/12-5-27-login-page.png " ")

28. In the rendering tab, Select Page Item **P9999_USERNAME**

    - Under Appearance > Value Placeholder: **Email Address or Username**

    ![Click My Apps](images/12-5-28-username-placeholder.png " ")

29. Under **Online Bookstore** region, right-click **Next** and select **Create Button**.

    ![Click My Apps](images/12-5-29-button-google.png " ")

30. In the Property editor, enter/select the following:

    - Under Identification:

        - Button Name: **Google**

        - Label: **Login/Signup with Google Account**

    - Under Appearance > Template options: Click **Use Template Defaults**

        - Size: **Large**

        Click **OK**.

    ![Click My Apps](images/12-5-30-google-prop1.png " ")

    - Under Behavior:

        - Action: **Redirect to page in this application**

        - Target: Click **No Link Defined**

            - Under Target > Page: **12**

            - Under Advanced > Request: **APEX_AUTHENTICATION=GOOGLE**

            Click **OK**.

    ![Click My Apps](images/12-5-30-google-prop2.png " ")

31. Navigate to **Processing** tab, right-click **Processing** and select **Create Process**.

    ![Click My Apps](images/12-5-31-process.png " ")

32. In the property editor, enter/select the following:

    - Under Identification:

        - Name: **Login with Google**

        - Type: **Invoke API**

    - Under Settings:

        - Package: **APEX\_AUTHENTICATION**

        - Procedure or Function: **SEND\_LOGIN\_USERNAME\_COOKIE**

    - Under Execution > Sequence: **25**

    - Under Server-side Condition > When Button Pressed: **Google**
    
    ![Click My Apps](images/12-5-32-process-details.png " ")

33. Select Login Process and update the following:

    - Under Server-side Condition > When Button Pressed: **LOGIN**

    ![Click My Apps](images/12-5-33-login-button.png " ")

34. Click **Save**.

## Summary

In this lab, you learned how to implement essential user authentication features to enhance the functionality and security of applications. Specifically, you explored how to send emails for communication or account verification, set up password reset functionality to allow users to recover their accounts, and integrate user signup and login processes using Google authentication. These skills are crucial for creating seamless and secure user experiences, ensuring users can easily access their accounts while maintaining robust security measures.

You are now ready to move on to the next lab!

## Acknowledgements

- **Author**: Pankaj Goyal, Member Technical Staff
- **Last Updated By/Date**: Pankaj Goyal, Member Technical Staff, Aug 2024