```sql
USE PAC_Policy;

BEGIN TRY

    BEGIN TRANSACTION;

    DECLARE @HistoryId BIGINT = 692950;
    DECLARE @NewValue DECIMAL(19,4) = 35.00;
    DECLARE @XmlData XML;

    -- ============================================================
    -- 1. Load the specific History record
    -- ============================================================

    SELECT @XmlData = CAST(XmlData AS XML)
    FROM History
    WHERE HistoryId = @HistoryId;


    -- ============================================================
    -- 2. Verify History record exists
    -- ============================================================

    IF @XmlData IS NULL
    BEGIN
        RAISERROR(
            'SAFETY CHECK FAILED: HistoryId 692950 was not found or XmlData is NULL. No changes were made.',
            16,
            1
        );

        ROLLBACK TRANSACTION;
        RETURN;
    END;


    -- ============================================================
    -- 3. SAFETY CHECK
    --
    -- We require EXACTLY:
    --
    -- Type        = Tail
    -- HistoryID   = 692950
    -- Charge      = 6
    -- TermPremium = 6
    -- NewPremium  = 6
    --
    -- If this exact transaction does not exist,
    -- NOTHING will be changed.
    -- ============================================================

    IF NOT EXISTS
    (
        SELECT 1
        FROM @XmlData.nodes('//transaction') AS T(N)
        WHERE
            T.N.value('(Type/text())[1]', 'nvarchar(50)') = 'Tail'
            AND
            T.N.value('(HistoryID/text())[1]', 'bigint') = @HistoryId
            AND
            T.N.value('(Charge/text())[1]', 'decimal(19,4)') = 6.0000
            AND
            T.N.value('(TermPremium/text())[1]', 'decimal(19,4)') = 6.0000
            AND
            T.N.value('(NewPremium/text())[1]', 'decimal(19,4)') = 6.0000
    )
    BEGIN
        RAISERROR(
            'SAFETY CHECK FAILED: Tail transaction 692950 is not currently Charge=6, TermPremium=6, NewPremium=6. No changes were made.',
            16,
            1
        );

        ROLLBACK TRANSACTION;
        RETURN;
    END;


    -- ============================================================
    -- 4. Update ONLY Charge
    -- ============================================================

    SET @XmlData.modify('
        replace value of
        (
            //transaction
            [Type="Tail" and HistoryID=sql:variable("@HistoryId")]
            /Charge/text()
        )[1]
        with sql:variable("@NewValue")
    ');


    -- ============================================================
    -- 5. Update ONLY TermPremium
    -- ============================================================

    SET @XmlData.modify('
        replace value of
        (
            //transaction
            [Type="Tail" and HistoryID=sql:variable("@HistoryId")]
            /TermPremium/text()
        )[1]
        with sql:variable("@NewValue")
    ');


    -- ============================================================
    -- 6. Update ONLY NewPremium
    -- ============================================================

    SET @XmlData.modify('
        replace value of
        (
            //transaction
            [Type="Tail" and HistoryID=sql:variable("@HistoryId")]
            /NewPremium/text()
        )[1]
        with sql:variable("@NewValue")
    ');


    -- ============================================================
    -- 7. Update ONLY History.XmlData
    -- ============================================================

    UPDATE History
    SET XmlData = CAST(@XmlData AS VARCHAR(MAX))
    WHERE HistoryId = @HistoryId;


    -- ============================================================
    -- 8. Verify BEFORE COMMIT
    -- ============================================================

    SELECT
        T.N.value('(Type/text())[1]', 'nvarchar(50)') AS TransactionType,
        T.N.value('(HistoryID/text())[1]', 'bigint') AS TransactionHistoryId,
        T.N.value('(Charge/text())[1]', 'decimal(19,4)') AS Charge,
        T.N.value('(TermPremium/text())[1]', 'decimal(19,4)') AS TermPremium,
        T.N.value('(NewPremium/text())[1]', 'decimal(19,4)') AS NewPremium
    FROM @XmlData.nodes('//transaction') AS T(N)
    WHERE
        T.N.value('(Type/text())[1]', 'nvarchar(50)') = 'Tail'
        AND
        T.N.value('(HistoryID/text())[1]', 'bigint') = @HistoryId;


    -- ============================================================
    -- 9. COMMIT
    -- ============================================================

    COMMIT TRANSACTION;

    SELECT
        'SUCCESS' AS Status,
        @HistoryId AS HistoryId,
        @NewValue AS NewValue,
        'Charge, TermPremium and NewPremium updated successfully.' AS Message;

END TRY

BEGIN CATCH

    IF @@TRANCOUNT > 0
        ROLLBACK TRANSACTION;

    SELECT
        ERROR_NUMBER() AS ErrorNumber,
        ERROR_MESSAGE() AS ErrorMessage,
        'ROLLBACK - NO CHANGES COMMITTED' AS Status;

END CATCH;
```








```sql
USE PAC_Policy;

DECLARE @HistoryId BIGINT = 692950;
DECLARE @XmlData XML;

SELECT @XmlData = CAST(XmlData AS XML)
FROM History
WHERE HistoryId = @HistoryId;

-- Check whether History row was found
SELECT
    CASE
        WHEN @XmlData IS NULL THEN 'XML DATA IS NULL / HISTORY ROW NOT FOUND'
        ELSE 'XML DATA LOADED'
    END AS Status;


-- Show ALL transactions from this History record
SELECT
    T.N.value('(Type/text())[1]', 'nvarchar(50)') AS TransactionType,
    T.N.value('(HistoryID/text())[1]', 'bigint') AS TransactionHistoryId,
    T.N.value('(Charge/text())[1]', 'decimal(19,4)') AS Charge,
    T.N.value('(TermPremium/text())[1]', 'decimal(19,4)') AS TermPremium,
    T.N.value('(NewPremium/text())[1]', 'decimal(19,4)') AS NewPremium
FROM @XmlData.nodes(
    '/session/data/policy/line/transactions/transaction'
) AS T(N);
```


``` sql
USE PAC_Policy;

SELECT
    HistoryId,
    CAST(XmlData AS VARCHAR(MAX)) AS XmlData
FROM History
WHERE HistoryId = 692950;
```

``` sql
USE PAC_Policy;

DECLARE @HistoryId BIGINT = 692950;
DECLARE @XmlData XML;

SELECT @XmlData = CAST(XmlData AS XML)
FROM History
WHERE HistoryId = @HistoryId;

SELECT
    T.N.value('(Type/text())[1]', 'nvarchar(50)') AS TransactionType,
    T.N.value('(HistoryID/text())[1]', 'bigint') AS TransactionHistoryId,
    T.N.value('(Charge/text())[1]', 'decimal(19,4)') AS Charge,
    T.N.value('(TermPremium/text())[1]', 'decimal(19,4)') AS TermPremium,
    T.N.value('(NewPremium/text())[1]', 'decimal(19,4)') AS NewPremium
FROM @XmlData.nodes('//transaction') AS T(N)
WHERE T.N.value('(HistoryID/text())[1]', 'bigint') = @HistoryId;
```
