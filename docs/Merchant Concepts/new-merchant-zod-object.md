---
title: New Merchant Zod Object
excerpt: >-
  You don't need to write your own form logic. This is what we use to validate
  the incoming new merchant data. 
deprecated: false
hidden: false
icon: far fa-key-skeleton
metadata:
  robots: index
---
You don't need to write your own form logic. This is what we use to validate the incoming new merchant data. Feel free to copy and paste it and use it to validate your own merchant form.

```typescript New Merchant Zod
import { z } from "zod";
import "zod-openapi/extend";
import { statesAbbrSchema } from "@/constants/states";
import { phoneNumberSchema } from "@/types/shared";

export const validateRoutingNumber = (routing: string) => {
  if (routing.length !== 9) return false;

  // Calculate checksum using ABA routing number algorithm
  const digits = routing.split("").map(Number);
  const checksum =
    3 * (digits[0]! + digits[3]! + digits[6]!) +
    7 * (digits[1]! + digits[4]! + digits[7]!) +
    1 * (digits[2]! + digits[5]! + digits[8]!);

  if (routing === "000000000") return false;

  return checksum % 10 === 0;
};

export const merchantPhoneNumberSchema = phoneNumberSchema.refine(
  (p) => p !== "+10000000000" && !p.startsWith("+1555") && p.slice(5, 8) !== "555",
  "Phone number cannot contain 555 or invalid format",
);

export const addressSchema = z.object({
  street1: z
    .string()
    .min(1, "Street address is required")
    .refine(
      (value) => !value.toLowerCase().includes("po box") && !value.toLowerCase().includes("p.o. box"),
      "PO Boxes are not allowed",
    ),
  street2: z.string().nullish(),
  city: z.string().min(1, "City is required"),
  state: statesAbbrSchema(),
  zipCode: z.string().regex(/^\d{5}(-\d{4})?$/, "ZIP code must be in format 12345 or 12345-6789"),
});

export const TITLE_ENUMS = ["CEO", "CFO", "COO", "President", "Secretary", "Treasurer", "Vice President"] as const;
export const titleEnumSchema = z.enum(TITLE_ENUMS, {
  errorMap: () => ({ message: "Invalid title" }),
});

export const ownershipTypeEnum = z.enum(
  [
    "GOVERNMENT",
    "JOINT STOCK",
    "LIMITED",
    "NON PROFIT ORG",
    "PARTNERSHIP",
    "CORPORATION",
    "PUBLIC COMPANY",
    "SOLE PROPRIETOR",
  ],
  {
    errorMap: () => ({ message: "Invalid business type" }),
  },
);

export const baseLegalSchema = z.object({
  name: z
    .string()
    .min(1, "Business legal name is required")
    .max(100, "Business legal name cannot exceed 100 characters"),
  dba: z.string().min(1, "DBA name is required").max(24, "DBA name cannot exceed 24 characters"),
  locationName: z.string().min(1, "Location name is required").nullish(),
  taxId: z.string().regex(/^\d{9}$/, "TIN/EIN/SSN must be in format XXXXXXXXX (9 digits)"),
  address: addressSchema,
  mailingAddressSameAsBusinessAddress: z.boolean().default(false),
  mailingAddress: addressSchema.partial().nullish(),
  ownershipType: ownershipTypeEnum,
  category: z
    .enum(["MOTO", "RETAIL"], {
      errorMap: () => ({ message: "Invalid business category" }),
    })
    .nullish(),
  productsSold: z.string().max(30, "Description cannot exceed 30 characters").nullish().default("Medical Services"),
  phone: merchantPhoneNumberSchema,
  email: z.string().email("Invalid business email address").min(1, "Business email is required"),
  dateOfIncorporation: z.coerce.date(),
  website: z.string().url("Must be a valid URL. It must begin with https://").nullish(),
  averageTicketPrice: z.number().min(0, "Average ticket price must be positive"),
  highTicketPrice: z.number().min(0, "High ticket price must be positive"),
  averageMonthlyVolume: z.number().min(0, "Average monthly volume must be positive"),
  averageYearlyVolume: z.number().min(0, "Average yearly volume must be positive").nullish(),
  b2bTransactionPercentage: z
    .number()
    .min(0, "B2B percentage must be between 0 and 100")
    .max(100, "B2B percentage must be between 0 and 100")
    .nullish(),
  b2cTransactionPercentage: z
    .number()
    .min(0, "B2C percentage must be between 0 and 100")
    .max(100, "B2C percentage must be between 0 and 100")
    .nullish(),
  cardPresentPercentage: z
    .number()
    .min(0, "cardPresent must be between 0 and 100")
    .max(100, "cardPresent must be between 0 and 100")
    .nullish(),
  cardNotPresentPercentage: z
    .number()
    .min(0, "cardNotPresent must be between 0 and 100")
    .max(100, "cardNotPresent must be between 0 and 100")
    .nullish(),
});

const legalSchema = baseLegalSchema
  .transform((data) => {
    const transformedData = { ...data };

    // Compute yearly volume if not provided
    if (!data.averageYearlyVolume) {
      transformedData.averageYearlyVolume = data.averageMonthlyVolume * 12;
    }

    // Handle B2B/B2C percentages
    if (data.b2bTransactionPercentage && !data.b2cTransactionPercentage) {
      transformedData.b2cTransactionPercentage = 100 - data.b2bTransactionPercentage;
    } else if (data.b2cTransactionPercentage && !data.b2bTransactionPercentage) {
      transformedData.b2bTransactionPercentage = 100 - data.b2cTransactionPercentage;
    }

    // Handle cp/cnp percentages
    if (data.cardPresentPercentage && !data.cardNotPresentPercentage) {
      transformedData.cardNotPresentPercentage = 100 - data.cardPresentPercentage;
    } else if (data.cardNotPresentPercentage && !data.cardPresentPercentage) {
      transformedData.cardPresentPercentage = 100 - data.cardNotPresentPercentage;
    }

    return transformedData;
  })
  .superRefine((data, ctx) => {
    // B2B/B2C: Validate that at least one percentage is provided
    if (!data.b2bTransactionPercentage && !data.b2cTransactionPercentage) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "Either B2B or B2C transaction percentage must be provided",
        path: ["b2bTransactionPercentage"],
      });
    }

    // B2B/B2C: Validate sum equals 100 if both provided
    if (
      data.b2bTransactionPercentage &&
      data.b2cTransactionPercentage &&
      data.b2bTransactionPercentage + data.b2cTransactionPercentage !== 100
    ) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "B2B and B2C percentages must sum to 100",
        path: ["b2bTransactionPercentage"],
      });
    }

    // CP/CNP: If provided validate sum equals 100 if both provided
    if (
      data.cardPresentPercentage &&
      data.cardNotPresentPercentage &&
      data.cardPresentPercentage + data.cardNotPresentPercentage !== 100
    ) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "CardPresent and CardNotPresent percentages must sum to 100",
        path: ["cardPresentPercentage"],
      });
    }

    // Business | Mailing Address validation
    if (!data.mailingAddressSameAsBusinessAddress) {
      const { error } = addressSchema.safeParse(data.mailingAddress ?? {});
      if (error) {
        error.errors.forEach((e) => {
          ctx.addIssue({
            code: e.code,
            message: e.message,
            path: e.path.map((p) => `mailingAddress.${p}`),
          } as IssueData);
        });
      }
    }
  });

export const ownerSchema = z.object({
  title: titleEnumSchema,
  firstName: z.string().min(1, "First Name is required"),
  lastName: z.string().min(1, "Last Name is required"),
  percentage: z
    .number()
    .min(0, "Ownership percentage must be positive")
    .max(100, "Ownership percentage cannot exceed 100")
    .refine((v) => v >= 25, {
      message: "Owner must have at least 25% ownership to be listed. Otherwise remove the owner",
    }),
  ssn: z.string().regex(/^\d{9}$/, "SSN must be in format XXXXXXXXX (9 digits)"),
  dob: z.coerce
    .date()
    // needs to be at least 18
    .refine((d) => d < new Date(Date.now() - 18 * 365 * 24 * 60 * 60 * 1000), {
      message: "Cannot be under 18 years old",
    }),
  address: addressSchema,
  phone: merchantPhoneNumberSchema,
  email: z.string().email("Invalid email address"),
  isControllingProng: z
    .boolean({
      required_error: "Controlling prong selection is required",
    })
    .default(false),
  isPrimaryContact: z
    .boolean({
      required_error: "Primary contact selection is required",
    })
    .default(false),
  isPciContact: z
    .boolean({
      required_error: "PCI contact selection is required",
    })
    .default(false),
});

const ownersSchema = z.array(ownerSchema).max(4, "Maximum of 4 owners allowed").superRefine((owners, ctx) => {
  owners.forEach((owner, index) => {
    if (owner.percentage < 25) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "Owner must have at least 25% ownership to be listed. Otherwise remove the owner",
        path: [index, "percentage"],
      });
    }
  });
});

// First define the control prong schema
export const controlProngSchema = z.object({
  title: titleEnumSchema,
  firstName: z.string().min(1, "First Name is required"),
  lastName: z.string().min(1, "Last Name is required"),
  ssn: z.string().regex(/^\d{9}$/, "SSN must be in format XXXXXXXXX (9 digits)"),
  dob: z.coerce.date().refine((d) => d < new Date(Date.now() - 18 * 365 * 24 * 60 * 60 * 1000), {
    message: "Cannot be under 18 years old",
  }),
  address: addressSchema,
  phone: phoneNumberSchema,
  email: z.string().email("Invalid email address"),
});

// First define the primary contact schema
export const primaryContactSchema = z.object({
  title: titleEnumSchema,
  firstName: z.string().min(1, "First Name is required"),
  lastName: z.string().min(1, "Last Name is required"),
  phone: phoneNumberSchema,
  email: z.string().email("Invalid email address"),
});

export const pciContactSchema = z.object({
  firstName: z.string().min(1, "First Name is required"),
  lastName: z.string().min(1, "Last Name is required"),
  phone: phoneNumberSchema,
  email: z.string().email("Invalid email address"),
});

export const bankAccountSchema = z
  .object({
    name: z.string().min(1, "Bank name is required"),
    routingNumber: z
      .string()
      .regex(/^\d{9}$/, "Routing number must be exactly 9 digits")
      .refine(validateRoutingNumber, "Invalid routing number"),
    confirmRoutingNumber: z.string(),
    accountNumber: z
      .string()
      .min(9, "Account number must be at least 9 characters")
      .regex(/^\d+$/, "Account number must contain only numbers"),
    confirmAccountNumber: z.string(),
  })
  .refine((data) => data.routingNumber === data.confirmRoutingNumber, {
    message: "Routing numbers must match",
    path: ["confirmRoutingNumber"],
  })
  .refine((data) => data.accountNumber === data.confirmAccountNumber, {
    message: "Account numbers must match",
    path: ["confirmAccountNumber"],
  });

export const baseMerchantRequestSchema = z.object({
  legal: legalSchema,
  owners: ownersSchema.nullish(),
  controlProng: controlProngSchema.nullish(),
  primaryContact: primaryContactSchema.nullish(),
  pciContact: pciContactSchema.nullish(),
  bankAccount: bankAccountSchema,
});

export const merchantRequestSchema = baseMerchantRequestSchema
  .transform((data) => {
    // Find owners with special roles
    const controllingOwner = data.owners?.find((owner) => owner.isControllingProng);
    const primaryContactOwner = data.owners?.find((owner) => owner.isPrimaryContact);
    const primaryPciContactOwner = data.owners?.find((owner) => owner.isPciContact);

    const transformedData = { ...data };

    // If primaryContact has been provided, then use that. Otherwise, use the primary contact owner
    if (primaryContactOwner && !data.primaryContact) {
      transformedData.primaryContact = {
        title: primaryContactOwner.title,
        firstName: primaryContactOwner.firstName,
        lastName: primaryContactOwner.lastName,
        phone: primaryContactOwner.phone,
        email: primaryContactOwner.email,
      };
    }

    // Autofill controlProng if controlling owner exists && this is not a sole proprietorship
    if (controllingOwner && data.legal.ownershipType !== "SOLE PROPRIETOR") {
      const {
        isControllingProng: _isControllingProng,
        percentage: _percentage,
        isPrimaryContact: _isPrimaryContact,
        isPciContact: _isPciContact,
        ...controlProngFields
      } = controllingOwner;
      transformedData.controlProng = controlProngFields;
    }

    if (primaryPciContactOwner && !data.pciContact) {
      transformedData.pciContact = {
        firstName: primaryPciContactOwner.firstName,
        lastName: primaryPciContactOwner.lastName,
        email: primaryPciContactOwner.email,
        phone: primaryPciContactOwner.phone,
      };
    }

    // type override to gurantee primaryContact existence.
    return transformedData as Omit<typeof transformedData, "primaryContact" | "pciContact"> & {
      primaryContact: NonNullable<(typeof transformedData)["primaryContact"]>;
      pciContact: NonNullable<(typeof transformedData)["pciContact"]>;
    };
  })
  .superRefine((values, ctx) => {
    let valid = true;
    const ownershipType = values.legal.ownershipType;

    // Rule 1. There should always be at least 1 primary contact
    if (!values.primaryContact) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "Primary contact information is required",
        path: ["primaryContact"],
      });
      valid = false;
    }

    if (!values.pciContact) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "PCI contact information is required",
        path: ["pciContact"],
      });
      valid = false;
    }

    new Set(values.owners?.map((o) => o.email) ?? []);
    if (new Set(values.owners?.map((o) => o.email) ?? []).size !== (values.owners ?? []).length) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: "Owners cannot have duplicate email",
        path: ["owners"],
      });
      valid = false;
    }

    if (["GOVERNMENT", "PUBLIC COMPANY"].includes(ownershipType)) {
      // Rule 2. If ownershipType is "GOVERNMENT" or "PUBLIC COMPANY", there should be no owners or control prong
      if (!!values.owners) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: `${ownershipType} should not have any owners listed`,
          path: ["owners"],
        });
        valid = false;
      }
      if (!!values.controlProng) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: `${ownershipType} should not have a control prong`,
          path: ["controlProng"],
        });
        valid = false;
      }
    }
    // Rule 3. If ownershipType is "NON PROFIT ORG", there should be no owners, but a control prong
    else if (ownershipType === "NON PROFIT ORG") {
      if (!!values.owners) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: "Non-profit organizations should not have any owners listed",
          path: ["owners"],
        });
        valid = false;
      }
      if (!values.controlProng) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: "Non-profit organizations require control prong information",
          path: ["controlProng"],
        });
        valid = false;
      }
    }
    // Rule 4. If ownershipType is "SOLE PROPRIETOR", there should be exactly 1 owner, no control prong, and legal name should equal firstName lastName
    else if (ownershipType === "SOLE PROPRIETOR") {
      if (!values.owners || values.owners.length !== 1) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: "Sole proprietorship must have exactly one owner",
          path: ["owners"],
        });
        valid = false;
      }
      if (!!values.controlProng) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: "Sole proprietorship should not have a control prong",
          path: ["controlProng"],
        });
        valid = false;
      }
      if (values.legal.name !== values.owners?.[0]?.firstName + " " + values.owners?.[0]?.lastName) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: "For sole proprietorship, business legal name must match owner's name",
          path: ["legal", "firstName"],
        });
        valid = false;
      }
    }
    // Rule 5. If its any other type of ownership, there should be at least 1 owner and a control prong
    else {
      if (!values.owners || values.owners.length === 0) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: `At least one owner is required for ${ownershipType}`,
          path: ["owners"],
        });
        valid = false;
      }
      if (!values.controlProng) {
        ctx.addIssue({
          code: z.ZodIssueCode.custom,
          message: "Control prong information is required when no owner is marked as controlling prong",
          path: ["controlProng"],
        });
        valid = false;
      }
    }

    return valid;
  });

export type MerchantRequestBody = z.infer<typeof merchantRequestSchema>;
```

```typescript @/constants/states
import { z } from "zod";

export const STATES = [
  {
    name: "Alabama",
    abbreviation: "AL",
  },
  {
    name: "Alaska",
    abbreviation: "AK",
  },
  {
    name: "American Samoa",
    abbreviation: "AS",
  },
  {
    name: "Arizona",
    abbreviation: "AZ",
  },
  {
    name: "Arkansas",
    abbreviation: "AR",
  },
  {
    name: "California",
    abbreviation: "CA",
  },
  {
    name: "Colorado",
    abbreviation: "CO",
  },
  {
    name: "Connecticut",
    abbreviation: "CT",
  },
  {
    name: "Delaware",
    abbreviation: "DE",
  },
  {
    name: "District Of Columbia",
    abbreviation: "DC",
  },
  {
    name: "Federated States Of Micronesia",
    abbreviation: "FM",
  },
  {
    name: "Florida",
    abbreviation: "FL",
  },
  {
    name: "Georgia",
    abbreviation: "GA",
  },
  {
    name: "Guam",
    abbreviation: "GU",
  },
  {
    name: "Hawaii",
    abbreviation: "HI",
  },
  {
    name: "Idaho",
    abbreviation: "ID",
  },
  {
    name: "Illinois",
    abbreviation: "IL",
  },
  {
    name: "Indiana",
    abbreviation: "IN",
  },
  {
    name: "Iowa",
    abbreviation: "IA",
  },
  {
    name: "Kansas",
    abbreviation: "KS",
  },
  {
    name: "Kentucky",
    abbreviation: "KY",
  },
  {
    name: "Louisiana",
    abbreviation: "LA",
  },
  {
    name: "Maine",
    abbreviation: "ME",
  },
  {
    name: "Marshall Islands",
    abbreviation: "MH",
  },
  {
    name: "Maryland",
    abbreviation: "MD",
  },
  {
    name: "Massachusetts",
    abbreviation: "MA",
  },
  {
    name: "Michigan",
    abbreviation: "MI",
  },
  {
    name: "Minnesota",
    abbreviation: "MN",
  },
  {
    name: "Mississippi",
    abbreviation: "MS",
  },
  {
    name: "Missouri",
    abbreviation: "MO",
  },
  {
    name: "Montana",
    abbreviation: "MT",
  },
  {
    name: "Nebraska",
    abbreviation: "NE",
  },
  {
    name: "Nevada",
    abbreviation: "NV",
  },
  {
    name: "New Hampshire",
    abbreviation: "NH",
  },
  {
    name: "New Jersey",
    abbreviation: "NJ",
  },
  {
    name: "New Mexico",
    abbreviation: "NM",
  },
  {
    name: "New York",
    abbreviation: "NY",
  },
  {
    name: "North Carolina",
    abbreviation: "NC",
  },
  {
    name: "North Dakota",
    abbreviation: "ND",
  },
  {
    name: "Northern Mariana Islands",
    abbreviation: "MP",
  },
  {
    name: "Ohio",
    abbreviation: "OH",
  },
  {
    name: "Oklahoma",
    abbreviation: "OK",
  },
  {
    name: "Oregon",
    abbreviation: "OR",
  },
  {
    name: "Palau",
    abbreviation: "PW",
  },
  {
    name: "Pennsylvania",
    abbreviation: "PA",
  },
  {
    name: "Puerto Rico",
    abbreviation: "PR",
  },
  {
    name: "Rhode Island",
    abbreviation: "RI",
  },
  {
    name: "South Carolina",
    abbreviation: "SC",
  },
  {
    name: "South Dakota",
    abbreviation: "SD",
  },
  {
    name: "Tennessee",
    abbreviation: "TN",
  },
  {
    name: "Texas",
    abbreviation: "TX",
  },
  {
    name: "Utah",
    abbreviation: "UT",
  },
  {
    name: "Vermont",
    abbreviation: "VT",
  },
  {
    name: "Virgin Islands",
    abbreviation: "VI",
  },
  {
    name: "Virginia",
    abbreviation: "VA",
  },
  {
    name: "Washington",
    abbreviation: "WA",
  },
  {
    name: "West Virginia",
    abbreviation: "WV",
  },
  {
    name: "Wisconsin",
    abbreviation: "WI",
  },
  {
    name: "Wyoming",
    abbreviation: "WY",
  },
] as const;

export type StatesAbbr = (typeof STATES)[number] extends object ? (typeof STATES)[number]["abbreviation"] : never;

export const statesAbbrSchema = (params?: z.RawCreateParams) =>
  z.enum<StatesAbbr, [StatesAbbr, ...StatesAbbr[]]>(
    STATES.map((s) => s.abbreviation) as [StatesAbbr, ...StatesAbbr[]],
    params,
  );

```

```typescript @/types/shared
import { z } from "zod";

export const phoneNumberSchema = z
  .string()
  .regex(/^\+(?:[0-9]){6,14}[0-9]$/, "Phone number needs to be in valid E.164 format")
  .refine(
    (v) => v.length === 12 && v.startsWith("+1"),
    "Country code or number is invalid. Currently supports +1 and requires 10 digit number",
  );

export type PhoneNumber = z.infer<typeof phoneNumberSchema>;

export const urlSchema = z
  .string()
  .regex(
    /^https?:\/\/(?:www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b(?:[-a-zA-Z0-9()@:%_\+.~#?&\/=]*)$/,
    { message: "Url is not valid" },
  );

```

<br />
