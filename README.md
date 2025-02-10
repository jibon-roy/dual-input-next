```bash
"use client";

import { usePathname } from "next/navigation";
import * as React from "react";
import { useCallback, useState } from "react";
import { HiChevronUp, HiChevronDown } from "react-icons/hi";
import { Sheet, SheetContent, SheetTrigger } from "@/components/ui/sheet";
import { Button } from "@/components/ui/button";
import { useDispatch } from "react-redux";
import { setclientFilter } from "@/redux/ReduxFunction";
import {
  setIndustry,
  setMaxBudget,
  setMaxRange,
  setMinBudget,
  setMinRange,
  setSkillType,
  setTimeline,
} from "@/redux/slice/Sidebar";

export type Filters = {
  industry: string[];
  timeline: string[];
  skillType: string[];
  projectMin: number;
  projectMax: number;
  minBudget: number;
  maxBudget: number;
};

export function Sidebar({
  setFilters,
}: {
  setFilters: React.Dispatch<React.SetStateAction<Filters>>;
}) {
  const [openSections, setOpenSections] = React.useState({
    industry: true,
    timeline: true,
    skillType: true,
  });

  const toggleSection = (section: keyof typeof openSections) => {
    setOpenSections((prev) => ({ ...prev, [section]: !prev[section] }));
  };

  const minGap = 0;
  const minGap2 = 0;
  const minPrice = 1;
  const maxPrice = 10000;
  const minDay = 1;
  const maxDay = 90;
  const minLoc = 10;
  const maxLoc = 10000;
  const dispatch = useDispatch();

  const [budgetMinValue, setBudgetMinValue] = useState(1);
  const [budgetMaxValue, setBudgetMaxValue] = useState(5000);
  const [durationMinValue, setDurationMinValue] = useState(1);
  const [durationMaxValue, setDurationMaxValue] = useState(90);
  const [locMinLoc, setMinLoc] = useState(10);
  const [locMaxLoc, setMaxLoc] = useState(5000);

  const handleBudgetMinChange = useCallback(
    (event: React.ChangeEvent<HTMLInputElement>) => {
      const value = Math.min(
        Number(event.target.value),
        budgetMaxValue - minGap
      );
      setBudgetMinValue(value);
      setFilters((prev) => ({ ...prev, minPrice: value }));
      dispatch(setMinBudget({ min: value }));
    },
    [budgetMaxValue, dispatch, setFilters]
  );

  const handleBudgetMaxChange = useCallback(
    (event: React.ChangeEvent<HTMLInputElement>) => {
      const value = Math.max(
        Number(event.target.value),
        budgetMinValue + minGap
      );
      setBudgetMaxValue(value);
      setFilters((prev) => ({ ...prev, maxPrice: value }));
      dispatch(setMaxBudget({ max: value }));
    },
    [budgetMinValue, dispatch, setFilters]
  );

  const getBudgetProgressStyle = useCallback(() => {
    const left = ((budgetMinValue - minPrice) / (maxPrice - minPrice)) * 100;
    const right =
      100 - ((budgetMaxValue - minPrice) / (maxPrice - minPrice)) * 100;
    return {
      left: `${left}%`,
      right: `${right}%`,
    };
  }, [budgetMinValue, budgetMaxValue]);

  const handleDurationMinChange = useCallback(
    (event: React.ChangeEvent<HTMLInputElement>) => {
      const value = Math.min(
        Number(event.target.value),
        durationMaxValue - minGap2
      );
      setDurationMinValue(value);
      setFilters((prev) => ({ ...prev, minDay: value }));
      dispatch(setMinRange({ min: value }));
    },
    [dispatch, durationMaxValue, setFilters]
  );

  const handleDurationMaxChange = useCallback(
    (event: React.ChangeEvent<HTMLInputElement>) => {
      const value = Math.max(
        Number(event.target.value),
        durationMinValue + minGap2
      );
      setDurationMaxValue(value);
      setFilters((prev) => ({ ...prev, maxDay: value }));
      dispatch(setMaxRange({ max: value }));
    },
    [dispatch, durationMinValue, setFilters]
  );

  const getDurationProgressStyle = useCallback(() => {
    const left = ((durationMinValue - minDay) / (maxDay - minDay)) * 100;
    const right = 100 - ((durationMaxValue - minDay) / (maxDay - minDay)) * 100;
    return {
      left: `${left}%`,
      right: `${right}%`,
    };
  }, [durationMinValue, durationMaxValue]);

  const pathName = usePathname();

  // const industry = ["tech", "marketing", "finance"]

  // add query params to the url

  // const { data: filterData } = useClientFilterListQuery({ industry: [], timeline: [], skillType: [] });
  // const { data: professionalfilterData } = useProfessionalFilterListQuery({ industry: [], timeline: [], skillType: [] });

  // console.log("filterData", professionalfilterData);
  // setclientFilter(filterData)

  // const [selectedFilters, setSelectedFilters] = useState<string[]>([]);

  const industryOptions = [
    { label: "EDUCATION", value: "EDUCATION" },
    { label: "ECOMMERCE", value: "ECOMMERCE" },
    { label: "REAL_ESTATE", value: "REAL_ESTATE" },
    { label: "ENTERTAINMENT", value: "ENTERTAINMENT" },
    { label: "TRAVEL", value: "TRAVEL" },
    { label: "AUTOMOTIVE", value: "AUTOMOTIVE" },
    { label: "MANUFACTURING", value: "MANUFACTURING" },
    { label: "FOOD", value: "FOOD" },
    { label: "FASHION", value: "FASHION" },
  ];

  const timelineOptions = [
    { label: "Short Term", value: "Short Term" },
    { label: "Long Term", value: "Long Term" },
  ];

  const skillTypeOptions = [
    {
      label: "Business consultancy and management",
      value: "Business consultancy and management",
    },
    { label: "Engineering services", value: "Engineering services" },
    { label: "Technical services", value: "Technical services" },
    {
      label: "Healthcare and medical consultancy",
      value: "Healthcare and medical consultancy",
    },
    { label: "Education and training", value: "Education and training" },
    {
      label: "Legal and financial services",
      value: "Legal and financial services",
    },
  ];

  type SelectedItems = {
    industry: string[];
    timeline: string[];
    skillType: string[];
  };

  const [selectedItems, setSelectedItems] = useState<SelectedItems>({
    industry: [],
    timeline: [],
    skillType: [],
  });

  const handleFilterChange = (section: keyof SelectedItems, value: string) => {
    // Update the local state for selected items
    const updatedSelection = { ...selectedItems };
    const sectionArray = updatedSelection[section];
    if (sectionArray.includes(value)) {
      updatedSelection[section] = sectionArray.filter((item) => item !== value);
    } else {
      updatedSelection[section] = [...sectionArray, value];
    }
    // Update local state
    setSelectedItems(updatedSelection);
    // Update parent state and dispatch action
    setFilters((prevFilters) => ({
      ...prevFilters,
      [section]: updatedSelection[section],
    }));

    dispatch(setclientFilter(updatedSelection));
    dispatch(setIndustry(updatedSelection.industry));
    dispatch(setTimeline(updatedSelection.timeline));
    dispatch(setSkillType(updatedSelection.skillType));
  };

  return (
    <div className="my-4 w-full max-w-md space-y-4 p-4 font-sans border rounded-[15px] lg:overflow-auto overflow-y-scroll">
      <div className="rounded-2xl border bg-white shadow-sm">
        <button
          onClick={() => toggleSection("industry")}
          className="flex w-full items-center justify-between p-4 text-left"
        >
          <h2 className="text-lg font-semibold">Industry</h2>
          {openSections.industry ? (
            <HiChevronUp className="h-5 w-5" />
          ) : (
            <HiChevronDown className="h-5 w-5" />
          )}
        </button>
        {openSections.industry && (
          <div className="space-y-3 p-4 pt-0">
            {industryOptions.map((option) => (
              <label key={option.value} className="flex items-center space-x-2">
                <input
                  type="checkbox"
                  checked={(selectedItems.industry as string[]).includes(
                    option.value
                  )}
                  onChange={() => handleFilterChange("industry", option.value)}
                  className="h-4 w-4 rounded border-gray-300"
                />
                <span className="font-medium">{option.label}</span>
              </label>
            ))}
          </div>
        )}
      </div>

      {/* Timeline Section */}
      <div className="rounded-2xl border bg-white shadow-sm">
        <button
          onClick={() => toggleSection("timeline")}
          className="flex w-full items-center justify-between p-4 text-left"
        >
          <h2 className="text-lg font-semibold">Timeline</h2>
          {openSections.timeline ? (
            <HiChevronUp className="h-5 w-5" />
          ) : (
            <HiChevronDown className="h-5 w-5" />
          )}
        </button>
        {openSections.timeline && (
          <div className="space-y-3 p-4 pt-0">
            {timelineOptions.map((option) => (
              <label key={option.value} className="flex items-center space-x-2">
                <input
                  type="checkbox"
                  checked={(selectedItems.timeline as string[]).includes(
                    option.value
                  )}
                  onChange={() => handleFilterChange("timeline", option.value)}
                  className="h-4 w-4 rounded border-gray-300"
                />
                <span className="font-medium">{option.label}</span>
              </label>
            ))}
          </div>
        )}
      </div>

      {/* Skill Type Section */}
      <div className="rounded-2xl border bg-white shadow-sm">
        <button
          onClick={() => toggleSection("skillType")}
          className="flex w-full items-center justify-between p-4 text-left"
        >
          <h2 className="text-lg font-semibold">Skill Type</h2>
          {openSections.skillType ? (
            <HiChevronUp className="h-5 w-5" />
          ) : (
            <HiChevronDown className="h-5 w-5" />
          )}
        </button>
        {openSections.skillType && (
          <div className="space-y-3 p-4 pt-0">
            {skillTypeOptions.map((option) => (
              <label key={option.value} className="flex items-center space-x-2">
                <input
                  type="checkbox"
                  checked={(selectedItems.skillType as string[]).includes(
                    option.value
                  )}
                  onChange={() => handleFilterChange("skillType", option.value)}
                  className="h-4 w-4 rounded border-gray-300"
                />
                <span className="font-medium">{option.label}</span>
              </label>
            ))}
          </div>
        )}
      </div>

      {pathName === "/project-list/retireProfessional" ? (
        <div className="grid grid-rows-2 gap-6 bg-white p-4 shadow-md rounded-[15px]">
          <div>
            <label className="block text-lg mb-4 font-medium">
              Project Duration Range
            </label>
            <div className="w-full py-8">
              <div className="relative h-2 mb-8">
                <div className="absolute w-full h-full bg-gray-200 rounded-full" />
                <div
                  className="absolute h-full bg-primary rounded-full"
                  style={getDurationProgressStyle()}
                />
                <input
                  type="range"
                  min={minDay}
                  max={maxDay}
                  value={durationMinValue}
                  onChange={handleDurationMinChange}
                  className="absolute w-full h-full appearance-none bg-transparent pointer-events-none [&::-webkit-slider-thumb]:pointer-events-auto [&::-webkit-slider-thumb]:w-5 [&::-webkit-slider-thumb]:h-5 [&::-webkit-slider-thumb]:appearance-none [&::-webkit-slider-thumb]:rounded-full [&::-webkit-slider-thumb]:bg-white [&::-webkit-slider-thumb]:shadow-md"
                />
                <input
                  type="range"
                  min={minDay}
                  max={maxDay}
                  value={durationMaxValue}
                  onChange={handleDurationMaxChange}
                  className="absolute w-full h-full appearance-none bg-transparent pointer-events-none [&::-webkit-slider-thumb]:pointer-events-auto [&::-webkit-slider-thumb]:w-5 [&::-webkit-slider-thumb]:h-5 [&::-webkit-slider-thumb]:appearance-none [&::-webkit-slider-thumb]:rounded-full [&::-webkit-slider-thumb]:bg-white [&::-webkit-slider-thumb]:shadow-md"
                />
              </div>
              <div className="w-full p-4 text-center border rounded-lg border-gray-200">
                {durationMinValue} Day - {durationMaxValue} Day
              </div>
            </div>
          </div>

          <div>
            <label className="block text-lg mb-4 font-medium">
              Budget Range
            </label>
            <div className="w-full py-8">
              <div className="relative h-2 mb-8">
                <div className="absolute w-full h-full bg-gray-200 rounded-full" />
                <div
                  className="absolute h-full bg-primary rounded-full"
                  style={getBudgetProgressStyle()}
                />
                <input
                  type="range"
                  min={minPrice}
                  max={maxPrice}
                  value={budgetMinValue}
                  onChange={handleBudgetMinChange}
                  className="absolute w-full h-full appearance-none bg-transparent pointer-events-none [&::-webkit-slider-thumb]:pointer-events-auto [&::-webkit-slider-thumb]:w-5 [&::-webkit-slider-thumb]:h-5 [&::-webkit-slider-thumb]:appearance-none [&::-webkit-slider-thumb]:rounded-full [&::-webkit-slider-thumb]:bg-white [&::-webkit-slider-thumb]:shadow-md"
                />
                <input
                  type="range"
                  min={minPrice}
                  max={maxPrice}
                  value={budgetMaxValue}
                  onChange={handleBudgetMaxChange}
                  className="absolute w-full h-full appearance-none bg-transparent pointer-events-none [&::-webkit-slider-thumb]:pointer-events-auto [&::-webkit-slider-thumb]:w-5 [&::-webkit-slider-thumb]:h-5 [&::-webkit-slider-thumb]:appearance-none [&::-webkit-slider-thumb]:rounded-full [&::-webkit-slider-thumb]:bg-white [&::-webkit-slider-thumb]:shadow-md"
                />
              </div>
              <div className="w-full p-4 text-center border rounded-lg border-gray-200">
                ${budgetMinValue} - ${budgetMaxValue}
              </div>
            </div>
          </div>
        </div>
      ) : (
        <div className="grid grid-rows-1 gap-6 bg-white p-4 shadow-md rounded-[15px]">
          <div>
            <label className="block text-lg mb-4 font-medium">Location</label>
            <div className="w-full py-8">
              <div className="relative h-2 mb-8">
                <div className="absolute w-full h-full bg-gray-200 rounded-full" />
                <div
                  className="absolute h-full bg-primary rounded-full"
                  style={getBudgetProgressStyle()}
                />
                <input
                  type="range"
                  min={minLoc}
                  max={maxLoc}
                  value={locMinLoc}
                  onChange={handleBudgetMinChange}
                  className="absolute w-full h-full appearance-none bg-transparent pointer-events-none [&::-webkit-slider-thumb]:pointer-events-auto [&::-webkit-slider-thumb]:w-5 [&::-webkit-slider-thumb]:h-5 [&::-webkit-slider-thumb]:appearance-none [&::-webkit-slider-thumb]:rounded-full [&::-webkit-slider-thumb]:bg-white [&::-webkit-slider-thumb]:shadow-md"
                />
                <input
                  type="range"
                  min={minLoc}
                  max={maxLoc}
                  value={locMaxLoc}
                  onChange={handleBudgetMaxChange}
                  className="absolute w-full h-full appearance-none bg-transparent pointer-events-none [&::-webkit-slider-thumb]:pointer-events-auto [&::-webkit-slider-thumb]:w-5 [&::-webkit-slider-thumb]:h-5 [&::-webkit-slider-thumb]:appearance-none [&::-webkit-slider-thumb]:rounded-full [&::-webkit-slider-thumb]:bg-white [&::-webkit-slider-thumb]:shadow-md"
                />
              </div>
              <div className="w-full p-4 text-center border rounded-lg border-gray-200">
                {budgetMinValue} Km - {budgetMaxValue} Km
              </div>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}

export function MobileSidebar({
  setFilters,
}: {
  setFilters: React.Dispatch<React.SetStateAction<Filters>>;
}) {
  return (
    <Sheet>
      <SheetTrigger asChild>
        <Button
          variant="outline"
          className="lg:hidden text-white rounded-[10px] mt-3 bg-primary "
        >
          Filters
        </Button>
      </SheetTrigger>
      <SheetContent side="left" className="w-[300px] sm:w-[400px] ">
        <Sidebar setFilters={setFilters} />
      </SheetContent>
    </Sheet>
  );
}
```
