
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
            RAISERROR('History record not found or XMLData is NULL.', 16, 1);
        END;

        SET @OriginalXml = @XmlText;

        -- Replace every occurrence of 3294 with 2040
        SET @XmlText = REPLACE(@XmlText, '3294', '2040');

        -- Check whether any replacement occurred
        IF @XmlText = @OriginalXml
        BEGIN
            RAISERROR('No occurrence of 3294 found in XML.', 16, 1);
        END;

        -- Validate modified XML
        SET @XmlCheck = TRY_CONVERT(XML, @XmlText);

        IF @XmlCheck IS NULL
        BEGIN
            RAISERROR('Updated XML is invalid. Changes rolled back.', 16, 1);
        END;

        -- Update XMLData
        UPDATE dbo.History
        SET XMLData = @XmlText
        WHERE HistoryId = @HistoryId
          AND PolicyNumber = @PolicyNumber;

        IF @@ROWCOUNT <> 1
        BEGIN
            RAISERROR('History XML update failed.', 16, 1);
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

        SELECT
            ERROR_NUMBER() AS ErrorNumber,
            ERROR_MESSAGE() AS ErrorMessage;

    END CATCH;
END;
GO
