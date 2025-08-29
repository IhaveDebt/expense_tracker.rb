#!/usr/bin/env ruby
# frozen_string_literal: true

# -----------------------------------------------------------
# Expense Tracker CLI
# -----------------------------------------------------------
# A professional Ruby utility to track personal expenses.
# Features:
# - Add, list, and summarize expenses
# - Saves data in CSV format for persistence
# - OOP design with clear methods and error handling
# - Polished command-line interface
#
# Author: Rainers
# GitHub: https://github.com/<your-username>
# -----------------------------------------------------------

require "csv"
require "date"

# Represents a single expense entry
class Expense
  attr_accessor :date, :category, :amount, :note

  def initialize(date, category, amount, note)
    @date = date
    @category = category
    @amount = amount.to_f
    @note = note
  end

  def to_a
    [@date, @category, @amount, @note]
  end
end

# Main tracker class
class ExpenseTracker
  FILE_PATH = "expenses.csv"

  def initialize
    @expenses = load_expenses
  end

  def add_expense
    print "Date (YYYY-MM-DD, leave blank for today): "
    date_input = gets.chomp
    date = date_input.empty? ? Date.today.to_s : date_input

    print "Category: "
    category = gets.chomp

    print "Amount: "
    amount = gets.chomp

    print "Note (optional): "
    note = gets.chomp

    expense = Expense.new(date, category, amount, note)
    @expenses << expense
    save_expenses
    puts "✅ Expense added successfully!"
  end

  def list_expenses
    puts "\nYour Expenses:"
    puts "-" * 50
    puts "Date       | Category        | Amount   | Note"
    puts "-" * 50
    @expenses.each do |exp|
      puts "#{exp.date} | #{exp.category.ljust(15)} | €#{format("%.2f", exp.amount)} | #{exp.note}"
    end
    puts "-" * 50
    puts "Total Expenses: €#{format("%.2f", total_expenses)}"
  end

  private

  def load_expenses
    return [] unless File.exist?(FILE_PATH)

    CSV.read(FILE_PATH, headers: true).map do |row|
      Expense.new(row["date"], row["category"], row["amount"], row["note"])
    end
  end

  def save_expenses
    CSV.open(FILE_PATH, "w", write_headers: true, headers: %w[date category amount note]) do |csv|
      @expenses.each { |exp| csv << exp.to_a }
    end
  end

  def total_expenses
    @expenses.sum(&:amount)
  end
end

# -----------------------------------------------------------
# CLI
# -----------------------------------------------------------
tracker = ExpenseTracker.new

loop do
  puts "\n💰 Expense Tracker"
  puts "1. Add Expense"
  puts "2. List Expenses"
  puts "3. Exit"
  print "Select an option: "
  choice = gets.chomp.to_i

  case choice
  when 1
    tracker.add_expense
  when 2
    tracker.list_expenses
  when 3
    puts "Goodbye!"
    break
  else
    puts "Invalid option, try again."
  end
end
