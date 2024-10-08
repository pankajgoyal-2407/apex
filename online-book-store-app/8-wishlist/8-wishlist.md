# Create and Manage Wishlist Functionality

## Introduction

This lab provides step-by-step instructions on how to create and manage a wishlist feature in Oracle APEX. You will learn to create a new page for the wishlist, add interactive components, and integrate navigation elements. The process includes creating dynamic regions, defining actions, and setting up processes for adding and removing items from the wishlist. Additionally, you will enhance the navigation bar to include a wishlist link for easy access.

Estimated Time: 15 minutes

### Objectives

In this lab, you will:

- Create a Wishlist Page.
- Add Dynamic Actions to refresh the wishlist content and handle interactions such as adding and removing items.
- Create Buttons to add and remove books from the wishlist, including the necessary backend processes.
- Update Navigation Bar to include a link to the wishlist page.

## Task 1: Create My Wishlist page

1. On Page Designer toolbar, Navigate to (+ v) and Select **Page**.

    ![close dialog](images/create-page-wishlist.png " ")

2. Select **Blank Page**.

    ![close dialog](images/order-info-blank.png " ")

3. In Create Blank Page dialog, enter/select the following:

    - Under Identification:

        - Page Number: **14**

        - Name: **My Wishlist**

    - Under Navigation:

        - Use Breadcrumb: **Toggle Off**

        - Use Navigation: **Toggle Off**

    Click **Create Page**.

   ![close dialog](images/wishlist-create-page.png " ")

4. In the left pane, right-click **Body** and Select **Create Region**.

    ![close dialog](images/wishlist-create-region.png " ")

5. In the Property Editor, enter/select the following:

    - Under Identification:

        - Title: **My books**

        - Type: **Cards**

    - Under Source:

        - Type: **SQL Query**

        - SQL Query: For the SQL Query, we will make use of the APEX Assistant to generate the query. Follow the next steps to generate the SQL query. Click the **Code Editor** icon.

    ![Page Designer](images/compute-sql.png =40%x*)

6. In the Code Editor, click **APEX Assistant** to open a drawer where you can chat with the AI Assistant. If a dialog box appears to accept the Terms and Conditions, click **Accept**.

    Enter the following prompt in the chat box and click **Send**:

    ```
    <copy>
    Help me create a query that returns some column from obs_wishlist and obs_books tables.

    book_id,
    author,
    book_image.
    title ,
    description,
    discount,
    price

    Calculate new_price after discount as well and round price and new_price by 2 decimal digits and filtering id of the user
    </copy>
    ```

    ![Page Designer](images/enter-prompt.png ' ')

7. The AI Assistant suggests a SQL Query. You can provide further prompts to refine the query. Once you are happy with the query, click **Insert**.

    ![Page Designer](images/insert-query.png ' ')

8. The SQL query is inserted into the Code Editor. Make sure *your user\_id* is **:USER\_ID**. Click **Validate**. The SQL query should look like the following:

    ```
    <copy>
    SELECT 
        ow.book_id,
        ob.author,
        ob.book_image,
        ob.title,
        ob.description,
        ob.discount,
        ROUND(ob.price, 2) AS price,
        ROUND(ob.price * ((100 - ob.discount) / 100), 2) AS new_price
    FROM 
        obs_wishlist ow
    JOIN 
        obs_books ob ON ow.book_id = ob.book_id
    WHERE 
        ow.user_id = :USER_ID;

    </copy>
    ```

    ![Page Designer](images/edit-validate.png ' ')

9. If the validation is successful, click **OK**.

    ![Page Designer](images/success-ok.png ' ')

10. Under Source > **Order By Item**: Enter/select the following:

    | Clause     |  Key   | Display |
    | ---------- |  ------ | ------ |
    | "TITLE"asc | TITLE |  Title |
    | "PRICE"asc | PRICE | Price |

    Click **OK**.

11. In the Property Editor, navigate to **Attributes** and enter/ select the following:

    - Appearance > Grid Columns: **5 Columns**

    - Card > Primary Key Column 1: **BOOK_ID**

    - Title > Column: **TITLE**

    - Under Body:

        - Advanced Formatting: **Toggle On**

        - HTML Expression: Copy and Paste the below HTML code:
        ```
       <copy>
       <div>
       <b>Author:</b> &AUTHOR.
       </div>

       <div>
       <b>Price:</b> <strike>₹&PRICE.</strike> ₹&NEW_PRICE.
       </div>

       <div >
       <b>Discount:</b> <span style="color: green;">&DISCOUNT.% Off</span>
       </div>
       </copy>
       ```
    - Under Media:

        - Source: **Image URL**

        - URL: **&BOOK_IMAGE.**

        - Image Description: **&DESCRIPTION.**

    ![close dialog](images/wishlist-att.png " ")

    ![close dialog](images/wishlist-att1.png " ")

12. Under **My Books** region, right-click **Actions** and click **Create Action**.

    ![close dialog](images/wishlist-action.png " ")

13. In the Property Editor, enter/select the following:

    - Identification > Type: **Full Card**

    - Link > Target: Click **No Link Defined**

        - Target > Page: **18**

        - Set Items > Name: **P18\_BOOK\_ID** and Value: **&BOOK\_ID.**

        -  Clear/Reset > Clear Cache: **18**

        Click **OK**.

    ![close dialog](images/wishlist-action-details.png " ")

14. Right-click **My Books** region and click **Create Page Item**.

    ![close dialog](images/wishtlist-page-item.png " ")

15. In the Property editor, enter/select the following:

    - Under Identification

        - Name: **P14\_BOOK\_ID**

        - Type: **Hidden**

    - Settings > Value protected: **Toggle Off**

    ![close dialog](images/wishlist-book-id.png " ")

16. Right-click **My Books** region and click **Create Dynamic Action**.

    ![close dialog](images/wishlist-dynamic-action.png " ")

17. In the Property editor, enter/select the following:

    - Identification > Name: **Refresh**

    - When > Event: **Dialog Closed**

    ![close dialog](images/refresh.png " ")

18. Select **TRUE** action and enter/select the following:

    - Identification > Action: **Refresh**

    - Under Affected Elements:

        - Selection Type: **Region**

        - Region: **My Books**

    ![close dialog](images/refresh-action.png " ")

19. Navigate to **Processing** tab, right-click **Processing** and click **Create Process**.

    ![close dialog](images/wishlist-create-process.png " ")

20. In the Property editor, enter/select the following:

     - Under Identification:

        - Name: **Remove from Wishlist**

        - Type: **Invoke API**
          
     - Under Settings : 

        - Package Name: **OBS\_MANAGE\_ORDERS**

        - Procedure/Function Name: **REMOVE\_FROM\_WISHLIST**

     - Under Server-side condition:

        - Type: **Request=Value**

        - Value: **REMOVE\_FROM\_WISHLIST**

      ![close dialog](images/remove-from-wishlist.png " ")

21. Under **Remove from Wishlist** process, expand parameters and enter/select the following:

     | Parameter     |  Type   | Item |
     | ---------- |  ------ | ------ |
     | p\_book\_id | Item |  P14\_BOOK\_ID |
     | p\_user\_id| Item | USER\_ID |

    ![close dialog](images/user-id-param.png " ")

22. Click **Save**.

## Task 2: Add Wishlist Management Buttons on Page 18

1. Navigate to Page **18**.

2. In the left pane, right-click **Buttons Bar** and click **Create Button**.

3. In the Property Editor, enter/select the following:

    - Under Identification:

        - Button Name: **Add\_to\_Wishlist**

        - Label: **Add to Wishlist**

    - Layout > Slot: **Next**

    - Under Appearance:

        - Button Template: **Text with Icon**

        - HOT: **Toggle On**

        - Template Options: Click **Use Template Defaults**

            - Type: Primary

            - Style: Simple

            Click Ok

        - Icon: **fa-heart-o**

    - Under Server-side Condition:

        - Type: **No rows returned**

        - SQL Query: Copy and Paste the below code into the code editor:
    ```
    <copy>
    select * from obs_wishlist where user_id = :user_id AND
    book_id = :P18_BOOK_ID
    </copy>
    ```

    - Under Security > Authorization Scheme: **Must Not Be Public User**

4. Right-click **Buttons Bar** and click **Create Button**.

5. In the Property Editor, enter/select the following:

    - Under Identification:

        - Button Name: **Remove\_from\_Wishlist**

        - Label: **Remove from Wishlist**

    - Under Layout > Slot: **Next**

    - Under Appearance:

        - Button Template: **Text with Icon** 

        - Icon: **fa-heart**

    - Under Behavior > Database Action: **SQL DELETE Action**

    - Under Server-side Condition:

        - Type: **Rows returned**

        - SQL Query: Copy and Paste the below code:
    ```
    <copy>
    select * from obs_wishlist where user_id = :user_id
    AND book_id = :P18_BOOK_ID
    </copy>
    ```

    - Under Security > Authorization Scheme: **Must Not Be Public User**

6. Navigate to **Processing** tab, right-click **Processing** and select **Create Process**.

7. In the Property Editor, enter/select the following:

    - Under Identification:

        - Name: **Add to Wishlist**

        - Type: **Invoke API**

    - Under Settings:

        - Package: **OBS\_MANAGE\_ORDERS**

        - Procedure or Function: **ADD\_TO\_WISHLIST**

    - Under Success Message > Success Message: **Successfully added to Wishlist!**

    - Under Error Message > Error Message: **Error adding to Wishlist!**

    - Under Server-side Condition > When button pressed: **Add\_to\_Wishlist**

8. Under **Add to Wishlist**, expand parameters and enter/select the following:

    - p\_book\_id > Under Value > Type: **Item**,  Item: **P18\_BOOK\_ID**

    - p\_user\_id > under value > Type: **Item**,  Item: **USER\_ID**

9. Right-click **Processing** and Click **Create Process**.

10. In the Property Editor, enter/select the following:

    - Under Identification:

        - Name: **Remove from Wishlist**

        - Type: **Invoke API**

    - Under Settings:

        - Package: **OBS\_MANAGE\_ORDERS**

        - Procedure or Function: **REMOVE\_FROM\_WISHLIST**

    - Under Server-side condition > When button pressed: **Remove\_from\_Wishlist**

11. Under **Remove From Whislist**, expand parameters and enter/select the following:

    - p\_book\_id > Under Value > Type: **Item**,  Item: **P18\_BOOK\_ID**

    - p\_user\_id > under value > Type: **Item**,  Item: **USER\_ID**

12. Drag and drop **Close Dialog** after **Remove from Wishlist** Process.

13. Click Save.

## Task 3: Create an entry in Navigation Bar List

1. Navigate to **Shared Component**.

    ![close dialog](images/sc-4.png " ")

2. Under Navigation and Search, click **Navigation Bar List**.

    ![close dialog](images/nav-bar-list1.png " ")

3. Click **Navigation Bar**.

    ![close dialog](images/nav-bar1.png " ")

4. Click **Create Entry**.

    ![close dialog](images/create-entery.png " ")

5. Enter/select the following:

    - Under Entry:

        - Sequence: **6**

        - Image/Class: **fa-heart**

        - List Entry Label: **My Wishlist**

    - Target > Page: **14**

      Click **Create List Entry**.

    ![close dialog](images/my-wishlist-nav.png " ")

6. Click Save and Run.

## Summary

In this lab, you successfully created a wishlist page. You designed a dynamic region to display books in the wishlist, added buttons for managing the wishlist, and implemented backend processes to handle wishlist modifications. Furthermore, you updated the navigation bar to include a link to the wishlist, ensuring easy access for users. This comprehensive exercise demonstrates how to integrate advanced functionalities into an Oracle APEX application, enhancing its usability and interactivity.

You are now ready to move on to the next lab!

## Acknowledgements

- **Author**: Pankaj Goyal, Member Technical Staff; Ankita Beri, Product Manager
- **Last Updated By/Date**: Pankaj Goyal, Member Technical Staff, Aug 2024
