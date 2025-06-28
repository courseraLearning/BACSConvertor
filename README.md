# BACSConvertor
// pom.xml dependencies
/*
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.12.0</version>
    </dependency>
</dependencies>
*/

// ======================================
// 1. Main Spring Boot Application Class
// ======================================

package com.example.bacsconverter;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BacsConverterApplication {
    public static void main(String[] args) {
        SpringApplication.run(BacsConverterApplication.class, args);
    }
}

// ======================================
// 2. BACS Data Model
// ======================================

package com.example.bacsconverter.model;

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.Pattern;
import javax.validation.constraints.Size;

public class BacsRecord {
    
    @NotBlank(message = "Record type is required")
    @Size(min = 1, max = 1, message = "Record type must be 1 character")
    private String recordType;
    
    @NotBlank(message = "Destination sort code is required")
    @Pattern(regexp = "\\d{6}", message = "Destination sort code must be 6 digits")
    private String destinationSortCode;
    
    @NotBlank(message = "Destination account is required")
    @Size(min = 8, max = 8, message = "Destination account must be 8 characters")
    private String destinationAccount;
    
    @NotBlank(message = "Amount is required")
    @Pattern(regexp = "\\d{1,11}", message = "Amount must be numeric and max 11 digits")
    private String amount;
    
    @NotBlank(message = "Originator sort code is required")
    @Pattern(regexp = "\\d{6}", message = "Originator sort code must be 6 digits")
    private String originatorSortCode;
    
    @NotBlank(message = "Originator account is required")
    @Size(min = 8, max = 8, message = "Originator account must be 8 characters")
    private String originatorAccount;
    
    @Size(max = 18, message = "Originator reference max 18 characters")
    private String originatorReference;
    
    @Size(max = 18, message = "Beneficiary reference max 18 characters")
    private String beneficiaryReference;
    
    @Pattern(regexp = "\\d{6}", message = "Processing date must be 6 digits (DDMMYY)")
    private String processingDate;
    
    // Constructors
    public BacsRecord() {}
    
    public BacsRecord(String recordType, String destinationSortCode, String destinationAccount,
                     String amount, String originatorSortCode, String originatorAccount,
                     String originatorReference, String beneficiaryReference, String processingDate) {
        this.recordType = recordType;
        this.destinationSortCode = destinationSortCode;
        this.destinationAccount = destinationAccount;
        this.amount = amount;
        this.originatorSortCode = originatorSortCode;
        this.originatorAccount = originatorAccount;
        this.originatorReference = originatorReference;
        this.beneficiaryReference = beneficiaryReference;
        this.processingDate = processingDate;
    }
    
    // Getters and Setters
    public String getRecordType() { return recordType; }
    public void setRecordType(String recordType) { this.recordType = recordType; }
    
    public String getDestinationSortCode() { return destinationSortCode; }
    public void setDestinationSortCode(String destinationSortCode) { this.destinationSortCode = destinationSortCode; }
    
    public String getDestinationAccount() { return destinationAccount; }
    public void setDestinationAccount(String destinationAccount) { this.destinationAccount = destinationAccount; }
    
    public String getAmount() { return amount; }
    public void setAmount(String amount) { this.amount = amount; }
    
    public String getOriginatorSortCode() { return originatorSortCode; }
    public void setOriginatorSortCode(String originatorSortCode) { this.originatorSortCode = originatorSortCode; }
    
    public String getOriginatorAccount() { return originatorAccount; }
    public void setOriginatorAccount(String originatorAccount) { this.originatorAccount = originatorAccount; }
    
    public String getOriginatorReference() { return originatorReference; }
    public void setOriginatorReference(String originatorReference) { this.originatorReference = originatorReference; }
    
    public String getBeneficiaryReference() { return beneficiaryReference; }
    public void setBeneficiaryReference(String beneficiaryReference) { this.beneficiaryReference = beneficiaryReference; }
    
    public String getProcessingDate() { return processingDate; }
    public void setProcessingDate(String processingDate) { this.processingDate = processingDate; }
}

// ======================================
// 3. ISO 8583 Message Model
// ======================================

package com.example.bacsconverter.model;

import java.util.Map;
import java.util.TreeMap;

public class Iso8583Message {
    
    private String messageTypeIndicator;
    private String bitmap;
    private Map<Integer, String> fields;
    private String rawMessage;
    
    public Iso8583Message() {
        this.fields = new TreeMap<>();
    }
    
    // Getters and Setters
    public String getMessageTypeIndicator() { return messageTypeIndicator; }
    public void setMessageTypeIndicator(String messageTypeIndicator) { this.messageTypeIndicator = messageTypeIndicator; }
    
    public String getBitmap() { return bitmap; }
    public void setBitmap(String bitmap) { this.bitmap = bitmap; }
    
    public Map<Integer, String> getFields() { return fields; }
    public void setFields(Map<Integer, String> fields) { this.fields = fields; }
    
    public String getRawMessage() { return rawMessage; }
    public void setRawMessage(String rawMessage) { this.rawMessage = rawMessage; }
    
    public void addField(int fieldNumber, String value) {
        this.fields.put(fieldNumber, value);
    }
}

// ======================================
// 4. Conversion Response DTO
// ======================================

package com.example.bacsconverter.dto;

import com.example.bacsconverter.model.BacsRecord;
import com.example.bacsconverter.model.Iso8583Message;

public class ConversionResponse {
    
    private BacsRecord originalBacsRecord;
    private Iso8583Message iso8583Message;
    private String status;
    private String message;
    
    public ConversionResponse() {}
    
    public ConversionResponse(BacsRecord originalBacsRecord, Iso8583Message iso8583Message, 
                            String status, String message) {
        this.originalBacsRecord = originalBacsRecord;
        this.iso8583Message = iso8583Message;
        this.status = status;
        this.message = message;
    }
    
    // Getters and Setters
    public BacsRecord getOriginalBacsRecord() { return originalBacsRecord; }
    public void setOriginalBacsRecord(BacsRecord originalBacsRecord) { this.originalBacsRecord = originalBacsRecord; }
    
    public Iso8583Message getIso8583Message() { return iso8583Message; }
    public void setIso8583Message(Iso8583Message iso8583Message) { this.iso8583Message = iso8583Message; }
    
    public String getStatus() { return status; }
    public void setStatus(String status) { this.status = status; }
    
    public String getMessage() { return message; }
    public void setMessage(String message) { this.message = message; }
}

// ======================================
// 5. BACS Parser Service
// ======================================

package com.example.bacsconverter.service;

import com.example.bacsconverter.model.BacsRecord;
import org.apache.commons.lang3.StringUtils;
import org.springframework.stereotype.Service;

@Service
public class BacsParserService {
    
    // BACS record field positions (start, length)
    private static final int RECORD_TYPE_START = 0;
    private static final int RECORD_TYPE_LENGTH = 1;
    
    private static final int DEST_SORT_CODE_START = 1;
    private static final int DEST_SORT_CODE_LENGTH = 6;
    
    private static final int DEST_ACCOUNT_START = 7;
    private static final int DEST_ACCOUNT_LENGTH = 8;
    
    private static final int AMOUNT_START = 15;
    private static final int AMOUNT_LENGTH = 11;
    
    private static final int ORIG_SORT_CODE_START = 26;
    private static final int ORIG_SORT_CODE_LENGTH = 6;
    
    private static final int ORIG_ACCOUNT_START = 32;
    private static final int ORIG_ACCOUNT_LENGTH = 8;
    
    private static final int ORIG_REFERENCE_START = 40;
    private static final int ORIG_REFERENCE_LENGTH = 18;
    
    private static final int BENEF_REFERENCE_START = 58;
    private static final int BENEF_REFERENCE_LENGTH = 18;
    
    private static final int PROCESSING_DATE_START = 76;
    private static final int PROCESSING_DATE_LENGTH = 6;
    
    public BacsRecord parseBacsRecord(String bacsRecordString) {
        if (StringUtils.isBlank(bacsRecordString) || bacsRecordString.length() < 82) {
            throw new IllegalArgumentException("BACS record must be at least 82 characters long");
        }
        
        BacsRecord bacsRecord = new BacsRecord();
        
        bacsRecord.setRecordType(extractField(bacsRecordString, RECORD_TYPE_START, RECORD_TYPE_LENGTH));
        bacsRecord.setDestinationSortCode(extractField(bacsRecordString, DEST_SORT_CODE_START, DEST_SORT_CODE_LENGTH));
        bacsRecord.setDestinationAccount(extractField(bacsRecordString, DEST_ACCOUNT_START, DEST_ACCOUNT_LENGTH));
        bacsRecord.setAmount(extractField(bacsRecordString, AMOUNT_START, AMOUNT_LENGTH));
        bacsRecord.setOriginatorSortCode(extractField(bacsRecordString, ORIG_SORT_CODE_START, ORIG_SORT_CODE_LENGTH));
        bacsRecord.setOriginatorAccount(extractField(bacsRecordString, ORIG_ACCOUNT_START, ORIG_ACCOUNT_LENGTH));
        bacsRecord.setOriginatorReference(extractField(bacsRecordString, ORIG_REFERENCE_START, ORIG_REFERENCE_LENGTH));
        bacsRecord.setBeneficiaryReference(extractField(bacsRecordString, BENEF_REFERENCE_START, BENEF_REFERENCE_LENGTH));
        bacsRecord.setProcessingDate(extractField(bacsRecordString, PROCESSING_DATE_START, PROCESSING_DATE_LENGTH));
        
        return bacsRecord;
    }
    
    private String extractField(String record, int start, int length) {
        if (start + length > record.length()) {
            return "";
        }
        return record.substring(start, start + length).trim();
    }
}

// ======================================
// 6. ISO 8583 Converter Service
// ======================================

package com.example.bacsconverter.service;

import com.example.bacsconverter.model.BacsRecord;
import com.example.bacsconverter.model.Iso8583Message;
import org.apache.commons.lang3.StringUtils;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Random;

@Service
public class Iso8583ConverterService {
    
    private final Random random = new Random();
    
    public Iso8583Message convertBacsToIso8583(BacsRecord bacsRecord) {
        Iso8583Message iso8583Message = new Iso8583Message();
        
        // Set Message Type Indicator (MTI)
        iso8583Message.setMessageTypeIndicator("0200"); // Financial Transaction Request
        
        // Add fields
        addField(iso8583Message, 2, formatPan(bacsRecord.getDestinationAccount()));
        addField(iso8583Message, 3, "000000"); // Processing code for credit transfer
        addField(iso8583Message, 4, formatAmount(bacsRecord.getAmount()));
        
        // Date/Time fields
        LocalDateTime now = LocalDateTime.now();
        addField(iso8583Message, 7, formatTransmissionDateTime(now));
        addField(iso8583Message, 11, generateTraceNumber());
        addField(iso8583Message, 12, formatLocalTime(now));
        addField(iso8583Message, 13, formatLocalDate(now));
        
        // Additional fields
        addField(iso8583Message, 18, "6012"); // MCC for financial institutions
        addField(iso8583Message, 22, "001");  // POS Entry Mode
        addField(iso8583Message, 25, "00");   // POS Condition Code
        addField(iso8583Message, 32, bacsRecord.getOriginatorSortCode());
        addField(iso8583Message, 37, formatReferenceNumber(bacsRecord.getOriginatorReference()));
        addField(iso8583Message, 41, "FP" + bacsRecord.getDestinationSortCode());
        addField(iso8583Message, 42, StringUtils.rightPad(bacsRecord.getOriginatorSortCode(), 15));
        addField(iso8583Message, 43, StringUtils.rightPad(StringUtils.defaultString(bacsRecord.getBeneficiaryReference()), 40));
        addField(iso8583Message, 49, "826"); // GBP currency code
        addField(iso8583Message, 102, bacsRecord.getOriginatorAccount());
        addField(iso8583Message, 103, bacsRecord.getDestinationAccount());
        
        // Generate bitmap and raw message
        iso8583Message.setBitmap(generateBitmap(iso8583Message));
        iso8583Message.setRawMessage(formatRawMessage(iso8583Message));
        
        return iso8583Message;
    }
    
    private void addField(Iso8583Message message, int fieldNumber, String value) {
        if (StringUtils.isNotBlank(value)) {
            message.addField(fieldNumber, value);
        }
    }
    
    private String formatPan(String account) {
        return StringUtils.leftPad(account, 16, '0');
    }
    
    private String formatAmount(String amount) {
        long amountValue = StringUtils.isNumeric(amount) ? Long.parseLong(amount) : 0L;
        return String.format("%012d", amountValue);
    }
    
    private String formatTransmissionDateTime(LocalDateTime dateTime) {
        return dateTime.format(DateTimeFormatter.ofPattern("MMddHHmmss"));
    }
    
    private String formatLocalTime(LocalDateTime dateTime) {
        return dateTime.format(DateTimeFormatter.ofPattern("HHmmss"));
    }
    
    private String formatLocalDate(LocalDateTime dateTime) {
        return dateTime.format(DateTimeFormatter.ofPattern("MMdd"));
    }
    
    private String generateTraceNumber() {
        return String.format("%06d", random.nextInt(999999) + 1);
    }
    
    private String formatReferenceNumber(String reference) {
        return StringUtils.rightPad(StringUtils.defaultString(reference), 12).substring(0, 12);
    }
    
    private String generateBitmap(Iso8583Message message) {
        byte[] bitmap = new byte[16]; // 128-bit bitmap
        
        for (Integer fieldNumber : message.getFields().keySet()) {
            if (fieldNumber > 1) { // Skip MTI and bitmap itself
                int bytePos = (fieldNumber - 1) / 8;
                int bitPos = 7 - ((fieldNumber - 1) % 8);
                bitmap[bytePos] |= (1 << bitPos);
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for (byte b : bitmap) {
            sb.append(String.format("%02X", b));
        }
        return sb.toString();
    }
    
    private String formatRawMessage(Iso8583Message message) {
        StringBuilder sb = new StringBuilder();
        
        // Add MTI
        sb.append(message.getMessageTypeIndicator());
        
        // Add bitmap
        sb.append(message.getBitmap());
        
        // Add fields in order
        for (Integer fieldNumber : message.getFields().keySet()) {
            String fieldValue = message.getFields().get(fieldNumber);
            
            // Add length indicator for variable fields
            if (isVariableLengthField(fieldNumber)) {
                sb.append(String.format("%02d", fieldValue.length()));
            }
            
            sb.append(fieldValue);
        }
        
        return sb.toString();
    }
    
    private boolean isVariableLengthField(int fieldNumber) {
        return fieldNumber == 2 || fieldNumber == 32 || fieldNumber == 37 || 
               fieldNumber == 43 || fieldNumber == 102 || fieldNumber == 103;
    }
}

// ======================================
// 7. Main Conversion Service
// ======================================

package com.example.bacsconverter.service;

import com.example.bacsconverter.dto.ConversionResponse;
import com.example.bacsconverter.model.BacsRecord;
import com.example.bacsconverter.model.Iso8583Message;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class BacsConversionService {
    
    @Autowired
    private BacsParserService bacsParserService;
    
    @Autowired
    private Iso8583ConverterService iso8583ConverterService;
    
    public ConversionResponse convertBacsToIso8583(String bacsRecordString) {
        try {
            // Parse BACS record
            BacsRecord bacsRecord = bacsParserService.parseBacsRecord(bacsRecordString);
            
            // Convert to ISO 8583
            Iso8583Message iso8583Message = iso8583ConverterService.convertBacsToIso8583(bacsRecord);
            
            return new ConversionResponse(bacsRecord, iso8583Message, "SUCCESS", "Conversion completed successfully");
            
        } catch (Exception e) {
            return new ConversionResponse(null, null, "ERROR", "Conversion failed: " + e.getMessage());
        }
    }
    
    public ConversionResponse convertBacsRecordToIso8583(BacsRecord bacsRecord) {
        try {
            // Convert to ISO 8583
            Iso8583Message iso8583Message = iso8583ConverterService.convertBacsToIso8583(bacsRecord);
            
            return new ConversionResponse(bacsRecord, iso8583Message, "SUCCESS", "Conversion completed successfully");
            
        } catch (Exception e) {
            return new ConversionResponse(bacsRecord, null, "ERROR", "Conversion failed: " + e.getMessage());
        }
    }
}

// ======================================
// 8. REST Controller
// ======================================

package com.example.bacsconverter.controller;

import com.example.bacsconverter.dto.ConversionResponse;
import com.example.bacsconverter.model.BacsRecord;
import com.example.bacsconverter.service.BacsConversionService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;

@RestController
@RequestMapping("/api/v1/bacs-converter")
@CrossOrigin(origins = "*")
public class BacsConverterController {
    
    @Autowired
    private BacsConversionService bacsConversionService;
    
    @PostMapping("/convert-string")
    public ResponseEntity<ConversionResponse> convertBacsString(@RequestBody String bacsRecordString) {
        ConversionResponse response = bacsConversionService.convertBacsToIso8583(bacsRecordString);
        
        if ("SUCCESS".equals(response.getStatus())) {
            return ResponseEntity.ok(response);
        } else {
            return ResponseEntity.badRequest().body(response);
        }
    }
    
    @PostMapping("/convert-object")
    public ResponseEntity<ConversionResponse> convertBacsObject(@Valid @RequestBody BacsRecord bacsRecord) {
        ConversionResponse response = bacsConversionService.convertBacsRecordToIso8583(bacsRecord);
        
        if ("SUCCESS".equals(response.getStatus())) {
            return ResponseEntity.ok(response);
        } else {
            return ResponseEntity.badRequest().body(response);
        }
    }
    
    @GetMapping("/health")
    public ResponseEntity<String> healthCheck() {
        return ResponseEntity.ok("BACS Converter Service is running");
    }
}

// ======================================
// 9. Configuration (application.yml)
// ======================================

/*
# application.yml
server:
  port: 8080
  servlet:
    context-path: /bacs-converter

spring:
  application:
    name: bacs-to-iso8583-converter
  
logging:
  level:
    com.example.bacsconverter: DEBUG
    org.springframework: INFO
  pattern:
    console: "%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n"

management:
  endpoints:
    web:
      exposure:
        include: health,info
  endpoint:
    health:
      show-details: always
*/

// ======================================
// 10. Sample Test Data and Usage
// ======================================

/*
Sample BACS Record String:
"1123456789012345000000012345654321987654321SALARY JAN2025    JOHN SMITH          250125"

Sample JSON for POST /api/v1/bacs-converter/convert-object:
{
  "recordType": "1",
  "destinationSortCode": "123456",
  "destinationAccount": "78901234",
  "amount": "123456",
  "originatorSortCode": "654321",
  "originatorAccount": "98765432",
  "originatorReference": "SALARY JAN2025",
  "beneficiaryReference": "JOHN SMITH",
  "processingDate": "250125"
}

Sample cURL commands:
curl -X POST http://localhost:8080/bacs-converter/api/v1/bacs-converter/convert-string \
  -H "Content-Type: application/json" \
  -d '"1123456789012345000000012345654321987654321SALARY JAN2025    JOHN SMITH          250125"'

curl -X POST http://localhost:8080/bacs-converter/api/v1/bacs-converter/convert-object \
  -H "Content-Type: application/json" \
  -d '{"recordType":"1","destinationSortCode":"123456","destinationAccount":"78901234","amount":"123456","originatorSortCode":"654321","originatorAccount":"98765432","originatorReference":"SALARY JAN2025","beneficiaryReference":"JOHN SMITH","processingDate":"250125"}'
*/
