USE PAC_Policy;
GO

CREATE OR ALTER PROCEDURE dbo.UpdateHistoryPremiumInXML
(
    @HistoryId BIGINT,
    @PolicyNumber NVARCHAR(50)
)
AS
BEGIN
    SET NOCOUNT ON;
    SET XACT_ABORT ON;

    DECLARE @XmlText VARCHAR(MAX);
    DECLARE @OriginalXml VARCHAR(MAX);
    DECLARE @XmlCheck XML;

    BEGIN TRY
        BEGIN TRANSACTION;

        -- Fetch and lock the requested History record
        SELECT
            @XmlText = CONVERT(VARCHAR(MAX), XMLData)
        FROM dbo.History WITH (UPDLOCK, HOLDLOCK)
        WHERE HistoryId = @HistoryId
          AND PolicyNumber = @PolicyNumber;

        IF @XmlText IS NULL
        BEGIN
            THROW 50001, 'History record not found or XMLData is NULL.', 1;
        END;

        SET @OriginalXml = @XmlText;

        -- Replace EVERY occurrence of 3294 with 2040
        SET @XmlText = REPLACE(@XmlText, '3294', '2040');

        -- Check whether any replacement occurred
        IF @XmlText = @OriginalXml
        BEGIN
            THROW 50002, 'No occurrence of 3294 found in XML.', 1;
        END;

        -- Validate the modified XML
        SET @XmlCheck = TRY_CONVERT(XML, @XmlText);

        IF @XmlCheck IS NULL
        BEGIN
            THROW 50003, 'Updated XML is invalid. Changes rolled back.', 1;
        END;

        -- Update XMLData
        UPDATE dbo.History
        SET XMLData = @XmlText
        WHERE HistoryId = @HistoryId
          AND PolicyNumber = @PolicyNumber;

        IF @@ROWCOUNT <> 1
        BEGIN
            THROW 50004, 'History XML update failed.', 1;
        END;

        COMMIT TRANSACTION;

        SELECT
            'Success' AS Status,
            @HistoryId AS HistoryId,
            @PolicyNumber AS PolicyNumber,
            'All occurrences of 3294 replaced with 2040.' AS Message;

    END TRY
    BEGIN CATCH

        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        THROW;

    END CATCH;
END;
GO


<img width="1645" height="340" alt="image" src="https://github.com/user-attachments/assets/719e036d-9656-470c-b6e6-dcd4c08f3c93" />
